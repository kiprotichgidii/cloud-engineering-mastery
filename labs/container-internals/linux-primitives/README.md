# Week 1: Linux kernel primitives behind containers

[← Back to Month 1: Container Internals and Runtime Engineering](../README.md)

## Weekly Overview
**Goal:** Deep technical investigation and hands-on laboratory exercises for Week 1.

### Why this week matters

Containers depend on kernel features that provide isolation and resource boundaries. A container runtime combines these features to create an execution environment for a process.

This week starts below the container-engine layer. You will use Linux commands and kernel interfaces directly to create isolated environments and observe what changes from the process's perspective.

The main concepts are:

- Namespaces: isolate a process's view of selected system resources (PID, UTS, mount, IPC, network).
- Mount operations & root filesystems: construct and control filesystem views with `pivot_root` and mount propagation.
- Virtual Ethernet devices: connect isolated network stacks using `veth` pairs and `ip netns`.
- `/proc` and `/sys`: expose process, namespace, and kernel control interfaces.
- Process IDs: understand how a process can have different PID representations at different namespace levels.
- cgroups v2: account for and enforce CPU, memory, and PID resource limits.
- Linux capabilities: constrain root privileges into fine-grained least-privilege boundaries.

## Hands-on Laboratory Modules
1. [**Lab 01 Linux Namespaces**](lab-01-linux-namespaces/README.md)
2. [**Lab 02 Minimal Rootfs**](lab-02-minimal-rootfs/README.md)
3. [**Lab 03 Cgroups V2**](lab-03-cgroups-v2/README.md)
4. [**Lab 04 Capabilities**](lab-04-capabilities/README.md)

### Detailed Curriculum Specifications
**[Lab 1.1 — Linux namespaces: understanding process isolation](lab-01-linux-namespaces/README.md)**
- Inspect existing host namespaces with `lsns` and `/proc/$$/ns/`.
- Create isolated UTS, PID, mount, network, and IPC namespaces using `unshare`.
- Mount a separate `/proc`, inspect process trees across host and namespace views, and test mount propagation (`--make-rprivate`).
- Connect two network namespaces (`ip netns`) with a `veth` pair, test connectivity, and capture ICMP traffic with `tcpdump`.
- Explore System V IPC isolation (`ipcmk`/`ipcs`) and execute deliberate failure-injection experiments.

**Lab 1.2 — Construct a minimal root filesystem**
- Create a minimal root filesystem using a small Linux distribution rootfs.
- Use `mount --bind`, `pivot_root` and mount namespaces to change the process root.
- Mount `/proc`, `/sys` and `/dev` appropriately.
- Run a process inside the new root.
- Demonstrate what a root filesystem does and does not isolate.
- Compare the result with a normal container.

**Lab 1.3 — Enforce resource limits with cgroups v2**
- Create a cgroup and move a test process into it.
- Configure CPU, memory and PID limits.
- Generate CPU and memory pressure.
- Inspect `cpu.stat`, `memory.current`, `memory.events` and `pids.current`.
- Observe throttling and OOM behavior.
- Compare host-level and cgroup-level measurements.

**Lab 1.4 — Linux capabilities and privilege boundaries**
- Inspect capabilities using `capsh` and `/proc`.
- Run processes with reduced capabilities.
- Test operations that require capabilities such as `NET_ADMIN` and `SYS_ADMIN`.
- Demonstrate why privileged containers are dangerous.
- Build a least-privilege capability profile for a sample service.

## Evidence to Commit
- Namespace and cgroup diagrams.
- Shell scripts for creating and cleaning the experiments.
- Output showing namespace identifiers and cgroup counters.
- A table mapping container features to Linux kernel primitives.

## Blog Article Target
**“Linux Namespaces Explained: Building Container Isolation from Scratch”** *(Week 1 series focus; broader theme: “Linux Containers Under the Hood: Namespaces, cgroups and the Kernel Primitives That Make Isolation Possible”)*

## Exit Criteria
Explain how namespaces isolate views of system resources, how cgroups account for and limit resources, and why neither mechanism alone provides a complete security boundary.
