# Lab 1.1 — Linux namespaces: understanding process isolation

[← Back to Week 1: Linux kernel primitives behind containers](../README.md) | [Month 1: Container Internals and Runtime Engineering](../../README.md)

**Estimated duration:** 4–5 hours  
**Difficulty:** Intermediate to advanced  
**Primary tools:** `unshare`, `nsenter`, `lsns`, `ip`, `ip netns`, `findmnt`, `ipcs`, `tcpdump`  
**Prerequisites:** Linux process management, basic networking, shell usage and sudo access

## Objective

Investigate how Linux namespaces isolate processes from selected host resources. Create namespaces manually, compare the host's view with the isolated process's view, connect isolated network stacks and gather evidence of the resulting behaviour.

By the end of this lab, you should be able to explain how several namespace types combine to provide the isolation expected from a container.

## Engineering context

Imagine running three applications on one Linux server:

- A web application.
- A database.
- A background worker.

You want each application to behave as though it has its own environment:

- The web application should not see unrelated host processes.
- The database should have its own hostname.
- The worker should have an independent network interface and IP address.
- Each application should have its own view of mounted filesystems.
- IPC objects should not automatically be shared across unrelated workloads.

Virtual machines can provide these boundaries, but running a separate kernel for every application is often unnecessary. Linux namespaces let processes share the host kernel while seeing different instances or views of selected resources.

**Important distinction:** Namespaces isolate views of resources. They do not, by themselves, limit CPU or memory consumption. Resource enforcement is the subject of [Lab 1.3](../lab-03-cgroups-v2/README.md).

## Namespace types

| Namespace | What it isolates | Container-related example |
|---|---|---|
| PID | Process IDs and process visibility | A process sees its own process tree |
| UTS | Hostname and domain name | Different container hostnames |
| Mount | Filesystem mount points | Independent filesystem views |
| IPC | System V IPC and POSIX message queues | Isolated inter-process communication |
| Network | Network devices, IP addresses, routes and ports | Container-specific network stack |
| User | User and group ID mappings and privilege context | Root inside a container without being host root |
| Cgroup | Cgroup view exposed to processes | A process sees a cgroup hierarchy relative to its namespace |

This lab focuses on PID, UTS, mount, IPC and network namespaces. User namespaces are introduced conceptually here and explored in greater depth in [Week 4 Lab 4.3 (Rootless containers)](../../container-security/lab-03-rootless/README.md) (while [Lab 1.4](../lab-04-capabilities/README.md) covers Linux capabilities and privilege boundaries). Cgroup namespaces may be investigated as an extension.

A process can belong to different namespace instances simultaneously. For example, it may have a private PID namespace, private network namespace, private mount namespace and private UTS namespace.

## Architecture

A namespace does not create a new kernel. The host kernel remains shared. The namespace changes what a process can see or interact with for the resource governed by that namespace.

Create a diagram in [`diagrams/namespace-architecture.mmd`](diagrams/namespace-architecture.mmd) illustrating:

- One shared Linux kernel.
- The host process namespace.
- Two isolated workload environments.
- Separate PID, UTS, mount and network namespace boundaries.
- The fact that the processes still use the same kernel.

Create another diagram in [`diagrams/veth-network.mmd`](diagrams/veth-network.mmd) illustrating two network namespaces connected by a veth pair.

## Exercise 1 — Inspect the host namespaces

Before creating anything, investigate the namespaces that already exist.

```bash
sudo lsns
```

This lists namespace instances visible on the system.

Inspect the namespaces associated with your current shell:

```bash
ls -l /proc/$$/ns/
```

You should see entries similar to:

```text
cgroup -> cgroup:[4026531835]
ipc    -> ipc:[4026531839]
mnt    -> mnt:[4026531841]
net    -> net:[4026531840]
pid    -> pid:[4026531836]
user   -> user:[4026531837]
uts    -> uts:[4026531838]
```

The identifiers are namespace inode numbers. Your actual values will differ.

Inspect one namespace:

```bash
readlink /proc/$$/ns/net
```

Compare it with PID 1:

```bash
readlink /proc/1/ns/net
```

If both return the same inode identifier, both processes are in the same network namespace.

Inspect all namespace identifiers for a process:

```bash
sudo lsns -p 1
```

### Investigation questions

1. What does it mean when two processes have the same namespace inode?
2. What changes when their namespace inode identifiers differ?
3. Why can two processes share a mount namespace but use different network namespaces?
4. Which namespace types are associated with your current shell?
5. Can a process join an existing namespace? Which tool can help you investigate this?

**Evidence:** Save the namespace identifier output and explain at least two comparisons in [`evidence/namespace-identifiers.txt`](evidence/namespace-identifiers.txt).

## Exercise 2 — Create a UTS namespace

The UTS namespace allows processes to have their own hostname and NIS domain name.

Start by checking the host hostname:

```bash
hostname
```

Create a new UTS namespace:

```bash
sudo unshare --uts /bin/bash
```

Inside the new shell, change the hostname:

```bash
hostname container-lab
hostname
```

Open another SSH session to the original VM and run:

```bash
hostname
```

The original host should retain its original hostname while the isolated shell reports `container-lab`.

Inspect the namespace identifiers from both sessions:

```bash
readlink /proc/$$/ns/uts
```

The inode identifiers should differ.

Exit the isolated shell:

```bash
exit
```

### What this demonstrates

Two processes can share the same Linux kernel while seeing different hostnames.

### Investigation questions

1. Why does changing the hostname inside the namespace not change the host hostname?
2. Does the new namespace create a new machine or a new kernel?
3. What would happen if you created another UTS namespace?
4. Which container runtime setting controls a container's hostname?
5. What permissions are required to change the hostname in a UTS namespace?

**Evidence:** Record the hostname and namespace inode from both sessions in [`evidence/namespace-identifiers.txt`](evidence/namespace-identifiers.txt).

## Exercise 3 — Create a PID namespace

This exercise demonstrates process visibility and PID isolation.

Create a new PID namespace and mount a new `/proc` filesystem:

```bash
sudo unshare --fork --pid --mount-proc /bin/bash
```

Inside the new shell:

```bash
echo $$
ps -ef
```

You should see the shell running with PID 1 inside the namespace.

Start a child process:

```bash
sleep 600 &
ps -ef
```

Open another SSH session to the host and run:

```bash
ps -ef | grep '[s]leep 600'
```

You should be able to see the host-side process even though it is not visible in the isolated namespace's process list.

Find the host-side process ID and inspect its namespace:

```bash
sudo lsns -t pid
```

Then inspect its status:

```bash
sudo cat /proc/HOST_PID/status
```

Replace `HOST_PID` with the actual host-side process ID.

### Important distinction

A process can have different PID representations at different namespace levels. It does not disappear from the host simply because it is hidden from another PID namespace.

The process at PID 1 inside a PID namespace is special. If it exits, the kernel terminates the remaining processes in that namespace.

### Investigation questions

1. Why does a container's main process often have PID 1?
2. What responsibilities does PID 1 have regarding child processes and signals?
3. Why can a container experience problems when its application does not handle signals or reap child processes correctly?
4. How does a small init process such as `tini` help?
5. What is the difference between a PID namespace and a process ID?

**Evidence:** Capture the process list inside the namespace and the corresponding host-side process information in [`evidence/pid-isolation.md`](evidence/pid-isolation.md). Explain the PID mapping.

## Exercise 4 — Create a mount namespace

The mount namespace isolates the mount table visible to processes.

Inspect the host mount table:

```bash
findmnt
```

Create a new mount namespace:

```bash
sudo unshare --mount /bin/bash
```

Inspect the mount table again:

```bash
findmnt
```

Initially, the new namespace may still show the same mounts as the host. Creating a mount namespace copies the existing mount list; it does not automatically create an empty filesystem.

Make mount propagation private:

```bash
mount --make-rprivate /
```

Create a test mount:

```bash
mkdir -p /tmp/ns-lab
mount -t tmpfs tmpfs /tmp/ns-lab
```

Verify:

```bash
findmnt /tmp/ns-lab
```

Open another SSH session on the host:

```bash
findmnt /tmp/ns-lab
```

The host should not see the mount created in the isolated namespace.

Exit the isolated shell:

```bash
exit
```

### What this demonstrates

A process can have a mount table different from the host's mount table.

### Why this matters

Container runtimes use mount namespaces to assemble the filesystem environment presented to a container process.

### Investigation questions

1. Why did the new namespace initially contain existing mounts?
2. What does `mount --make-rprivate /` change?
3. What is the difference between private, shared, slave and unbindable mount propagation?
4. Why is mount propagation important when containers use host-mounted volumes?
5. How does this relate to bind mounts in Docker or Podman?

**Evidence:** Record `findmnt` comparisons from inside the mount namespace and on the host in [`evidence/mount-isolation.md`](evidence/mount-isolation.md).

**Safety:** Perform this exercise only inside the disposable VM. Do not experiment with mount propagation on the primary host or a production Kubernetes node.

## Exercise 5 — Create a network namespace

Create a network namespace:

```bash
sudo unshare --net /bin/bash
```

Inspect network interfaces and routes:

```bash
ip addr
ip route
```

You will typically see only the loopback interface, and it may initially be down.

Bring it up:

```bash
ip link set lo up
ip addr
```

Compare with the host network:

```bash
ip addr
ip route
```

The isolated shell has its own network stack. It does not automatically receive an IP address or internet connectivity.

### Investigation questions

1. Why does the new namespace not inherit the host's Ethernet interface?
2. Why is the loopback interface initially down?
3. What is isolated by a network namespace?
4. How could you connect this namespace to the host network?
5. How do Docker and Podman provide network connectivity to containers?

**Evidence:** Record the interface and route output from both the host and isolated namespace in [`evidence/network-connectivity.md`](evidence/network-connectivity.md).

## Exercise 6 — Connect two network namespaces using veth

A veth pair acts like a virtual Ethernet cable: packets transmitted on one end appear on the other end. *(You can automate these setup and connectivity checks in [`scripts/create-network-namespaces.sh`](scripts/create-network-namespaces.sh) and [`scripts/test-connectivity.sh`](scripts/test-connectivity.sh).)*

Create two network namespaces:

```bash
sudo ip netns add ns-app
sudo ip netns add ns-db
```

Create a virtual Ethernet pair:

```bash
sudo ip link add veth-app type veth peer name veth-db
```

Move each end into its respective namespace:

```bash
sudo ip link set veth-app netns ns-app
sudo ip link set veth-db netns ns-db
```

Assign IP addresses:

```bash
sudo ip -n ns-app addr add 10.10.10.1/24 dev veth-app
sudo ip -n ns-db addr add 10.10.10.2/24 dev veth-db
```

Bring up both interfaces and loopback:

```bash
sudo ip -n ns-app link set lo up
sudo ip -n ns-db link set lo up

sudo ip -n ns-app link set veth-app up
sudo ip -n ns-db link set veth-db up
```

Inspect the interfaces:

```bash
sudo ip -n ns-app addr
sudo ip -n ns-db addr
```

Test connectivity:

```bash
sudo ip netns exec ns-app ping -c 4 10.10.10.2
sudo ip netns exec ns-db ping -c 4 10.10.10.1
```

Inspect route tables:

```bash
sudo ip -n ns-app route
sudo ip -n ns-db route
```

Capture packets:

```bash
sudo ip netns exec ns-app tcpdump -ni veth-app icmp
```

In another terminal, run the ping again.

Observe the ICMP echo request and reply.

### What you have demonstrated

You now have two isolated network stacks communicating through a virtual Ethernet link.

### Investigation questions

1. What happens if you bring down `veth-db`?
2. What happens if you assign both interfaces addresses from different subnets?
3. Why can the two namespaces communicate without a physical network interface?
4. What additional components are required to connect these namespaces to the outside network?
5. How would a Linux bridge fit into a container network design?
6. What role do routing, NAT and conntrack play in providing external connectivity?

**Evidence:** Save interface configuration, route tables, and ping output in [`evidence/network-connectivity.md`](evidence/network-connectivity.md), and save the packet capture or annotated `tcpdump` output in [`evidence/packet-capture.md`](evidence/packet-capture.md).

## Exercise 7 — Explore IPC isolation

IPC namespaces isolate System V IPC objects and POSIX message queues.

Create an IPC namespace:

```bash
sudo unshare --ipc /bin/bash
```

Inspect System V IPC objects:

```bash
ipcs -a
```

Create a shared-memory segment:

```bash
ipcmk -M 4096
ipcs -m
```

Exit the namespace and inspect the host:

```bash
ipcs -m
```

Compare the results. The host and isolated namespace should not have the same view of IPC objects created within their respective IPC namespaces.

Investigate message queues and semaphores:

```bash
ipcs -q
ipcs -s
```

### Why this matters

Applications sometimes use shared memory, semaphores and message queues for inter-process communication. Containers need isolation boundaries around these mechanisms too.

### Investigation questions

1. Which IPC mechanisms are covered by an IPC namespace?
2. How does a shared-memory segment differ from a message queue?
3. Why might an application fail when it expects an IPC object that is outside its namespace?
4. How would you investigate an IPC-related application failure?

**Evidence:** Record IPC object listings before and after creating an object inside the namespace in [`evidence/namespace-identifiers.txt`](evidence/namespace-identifiers.txt).

## Failure injection and troubleshooting

Do not finish this lab simply because the commands work. Deliberately introduce failures, diagnose them, and document your findings in [`troubleshooting.md`](troubleshooting.md).

| Experiment | Action | Expected observation |
|---|---|---|
| Hostname isolation | Change hostname in UTS namespace | Hostname on host remains unchanged |
| PID visibility | Inspect host and isolated process lists | Different PID views |
| Mount propagation | Create a mount after making propagation private | Host does not see the new mount |
| Network isolation | Create a network namespace | No ordinary external connectivity |
| veth failure | Bring down one veth interface | Namespace-to-namespace ping fails |
| IP addressing | Configure incompatible subnets | Direct communication fails |
| IPC isolation | Create IPC object in separate namespace | Object is not visible from unrelated IPC namespaces |

For every failure, record:

1. What you changed.
2. What you expected to happen.
3. What actually happened.
4. The command or evidence that identified the issue.
5. The root cause.
6. How you restored the working state.

## Cleanup

You can automate cleanup with [`scripts/cleanup.sh`](scripts/cleanup.sh), or run the teardown commands manually.

Delete the network namespaces:

```bash
sudo ip netns delete ns-app
sudo ip netns delete ns-db
```

Exit all experimental shells and verify that no lab processes remain:

```bash
sudo lsns
ps -ef | grep '[s]leep 600'
```

If you created IPC objects outside a disposable namespace, remove them using their IDs and the appropriate `ipcrm` command.

If a test mount remains in an experimental namespace, unmount it from that namespace before exiting. Do not blindly unmount paths on the host.

## Lab completion checklist

- [ ] Capture the host namespace inode identifiers.
- [ ] Create and verify a UTS namespace.
- [ ] Create and inspect a PID namespace.
- [ ] Demonstrate mount namespace isolation.
- [ ] Create an isolated network namespace.
- [ ] Connect two network namespaces using a veth pair.
- [ ] Capture and explain ICMP traffic with `tcpdump`.
- [ ] Demonstrate IPC namespace isolation.
- [ ] Complete at least three deliberate failure experiments.
- [ ] Document cleanup and reproduce the lab from a clean state.

## GitHub deliverables

Populate the lab deliverables under this directory:

```text
labs/
└── container-internals/
    └── linux-primitives/
        └── lab-01-linux-namespaces/
            ├── README.md
            ├── scripts/
            │   ├── create-network-namespaces.sh
            │   ├── test-connectivity.sh
            │   └── cleanup.sh
            ├── evidence/
            │   ├── namespace-identifiers.txt
            │   ├── pid-isolation.md
            │   ├── mount-isolation.md
            │   ├── network-connectivity.md
            │   └── packet-capture.md
            ├── diagrams/
            │   ├── namespace-architecture.mmd
            │   └── veth-network.mmd
            └── troubleshooting.md
```

Do not commit empty placeholder output—update each file as you produce useful, verified material.

## Blog article

**Suggested title:** Linux Namespaces Explained: Building Container Isolation from Scratch

The article should answer:

- How does Linux isolate processes without virtual machines?
- What does each namespace isolate?
- How do processes see different PIDs and hostnames?
- How does a mount namespace give a process a different filesystem view?
- How do veth pairs connect isolated network stacks?
- What are the limitations of namespaces alone?

Recommended article structure:

1. Problem and motivation.
2. Linux namespace architecture.
3. Lab environment and versions.
4. Inspecting namespace identifiers.
5. UTS and PID experiments.
6. Mount namespace and propagation experiment.
7. Network namespace and veth connectivity.
8. IPC namespace experiment.
9. Failure injection and troubleshooting.
10. Findings, limitations and security considerations.
11. Reproduction and cleanup.
12. References.

Include original diagrams, commands, selected outputs and explanations. Distinguish what you observed from what you inferred.

### References

- [namespaces(7) — Linux manual](https://man7.org/linux/man-pages/man7/namespaces.7.html)
- [unshare(1) — Linux manual](https://man7.org/linux/man-pages/man1/unshare.1.html)
- [ip-netns(8) — Linux manual](https://man7.org/linux/man-pages/man8/ip-netns.8.html)
- [pid_namespaces(7) — Linux manual](https://man7.org/linux/man-pages/man7/pid_namespaces.7.html)
- [Linux kernel namespace documentation](https://docs.kernel.org/admin-guide/namespaces/index.html)

---