# Lab 1.4 — Linux Capabilities and Container Privilege Boundaries

> **Module:** Month 1 — Container Internals and Runtime Engineering  
> **Week:** 1 — Linux Kernel Primitives  
> **Estimated effort:** 5–7 hours | **Difficulty:** Intermediate / advanced  
> **Environment:** Disposable Ubuntu Server 24.04 or Debian 13 VM on Proxmox  
> **Tools:** `capsh`, `getcap`, `setcap`, `getpcaps`, `setpriv`, `unshare`, Docker or Podman

## 1. Objective

Investigate how Linux capabilities divide traditional root privileges into smaller units, how capabilities are represented for processes, and how container runtimes use capability restrictions to reduce workload privilege.

You will inspect capability sets, compare root and non-root execution, drop selected capabilities, examine file capabilities, explore user-namespace UID/GID mappings, and compare rootful and rootless containers.

## 2. Engineering context

Traditional Unix privilege is often described as UID 0 versus non-zero UID. Linux capabilities make the model more granular. A process can have UID 0 while lacking selected capabilities; a non-root process can also be granted a limited capability.

Container runtimes use capabilities as one layer of workload hardening. A workload can run as non-root, drop capabilities it does not need, prevent privilege escalation, use seccomp to restrict system calls, and be confined by AppArmor or SELinux.

No single control provides complete isolation. Capabilities must be understood alongside namespaces, cgroups, seccomp, LSMs, user namespaces and filesystem permissions.

## 3. Learning outcomes

By the end, you should be able to:
- Inspect process capability sets.
- Explain permitted, effective, inheritable, bounding and ambient sets.
- Explain why `CAP_SYS_ADMIN` is broad and sensitive.
- Demonstrate the effect of dropping a capability.
- Understand file capabilities.
- Read UID/GID mappings in user namespaces.
- Compare rootful and rootless containers.
- Configure a least-privilege container.
- Explain `no-new-privileges` and its relationship to capability restrictions.

## 4. Safety

Use only the disposable VM and take a Proxmox snapshot.

- Do not alter capabilities of PID 1, SSH or system services.
- Do not perform privilege experiments on production nodes.
- Do not grant `CAP_SYS_ADMIN` as a quick fix.
- Avoid `--privileged` except for a carefully reviewed comparison in the lab VM.
- Use temporary binaries and test namespaces.
- Remove file capabilities and test users after the lab.

## 5. Preparation

Install:

```bash
sudo apt update
sudo apt install -y libcap2-bin libcap-dev util-linux procps
```

Verify:

```bash
command -v capsh
command -v getcap
command -v setcap
command -v getpcaps
command -v setpriv
uname -a
```

---

## Exercise 1 — Inspect process capability sets

Inspect your shell:

```bash
id
grep '^Cap' /proc/$$/status
capsh --print
```

Inspect PID 1 without changing it:

```bash
sudo getpcaps 1
sudo grep '^Cap' /proc/1/status
```

The `/proc/<pid>/status` capability values are hexadecimal bitmasks. `capsh --print` helps decode the current process's capabilities.

| Set | Meaning |
|---|---|
| Permitted (`CapPrm`) | Capabilities the process may make effective |
| Effective (`CapEff`) | Capabilities currently used for permission checks |
| Inheritable (`CapInh`) | Capabilities participating in inheritance across `execve` |
| Bounding (`CapBnd`) | Limits capabilities that may be gained through certain execution transitions |
| Ambient (`CapAmb`) | Capabilities preserved across execution of non-privileged programs under defined conditions |

Capability transitions across `fork`, `execve`, UID changes and file capabilities have detailed rules. Do not reduce the model to a simple on/off flag.

**Investigate:** Which capabilities are effective? Are permitted and effective identical? Why might a capability be permitted but not effective? What does the bounding set restrict? How can capabilities change after `execve`?

Save output in `evidence/current-shell-capabilities.txt`.

## Exercise 2 — Compare root and non-root processes

Inspect root's capabilities:

```bash
sudo capsh --print
```

Create a dedicated test user:

```bash
sudo useradd --create-home --shell /bin/bash cap-lab
```

Inspect a shell as that user:

```bash
sudo -u cap-lab -H bash
id
grep '^Cap' /proc/$$/status
capsh --print
exit
```

Compare UID/GID and capability sets.

UID 0 often receives a broad set in the initial user namespace, but the exact set depends on context, namespace membership, runtime configuration and security policy. Root inside a user namespace is not equivalent to root in the initial host user namespace.

**Investigate:** Does UID 0 always have every capability? Can a non-zero UID possess a capability? What changes when root is inside a user namespace? Why should application containers normally run as non-root where practical?

## Exercise 3 — Drop CAP_NET_RAW and test the underlying operation

`CAP_NET_RAW` governs use of raw and packet sockets. The behaviour of `ping` varies by distribution because it may use unprivileged datagram sockets or file capabilities. Test raw socket creation directly rather than relying only on ping.

Run as root:

```bash
sudo python3 - <<'PY'
import socket
try:
    s = socket.socket(socket.AF_INET, socket.SOCK_RAW, socket.IPPROTO_ICMP)
    print("raw socket created")
    s.close()
except OSError as exc:
    print(f"raw socket denied: {exc}")
PY
```

Repeat with the capability dropped:

```bash
sudo capsh --drop=cap_net_raw -- -c "python3 - <<'PY'
import socket
try:
    s = socket.socket(socket.AF_INET, socket.SOCK_RAW, socket.IPPROTO_ICMP)
    print('raw socket created')
    s.close()
except OSError as exc:
    print(f'raw socket denied: {exc}')
PY"
```

Inspect the resulting process capability set:

```bash
sudo capsh --drop=cap_net_raw -- -c 'capsh --print'
```

If both tests are denied, investigate other controls such as seccomp, LSM policy or the execution context. Record the result rather than claiming the capability test succeeded.

**Investigate:** What exact operation does `CAP_NET_RAW` control? What error appears when absent? Why can ping behave differently? Why is testing the underlying operation more reliable than inferring from an application?

## Exercise 4 — Investigate CAP_NET_ADMIN in a network namespace

`CAP_NET_ADMIN` permits a range of network administration operations in the relevant user/network namespace context. Ordinary application containers generally do not need to configure their own network; the runtime and CNI plugin do that work.

Create a disposable namespace:

```bash
sudo ip netns add cap-net-lab
sudo ip -n cap-net-lab link set lo up
sudo ip -n cap-net-lab link show
```

Attempt to create a dummy interface:

```bash
sudo ip netns exec cap-net-lab ip link add dummy0 type dummy
sudo ip -n cap-net-lab link show
```

Compare with a process that has `CAP_NET_ADMIN` dropped:

```bash
sudo ip netns exec cap-net-lab \
  capsh --drop=cap_net_admin -- -c 'ip link add dummy1 type dummy'
```

Expected outcome is denial if the process lacks the required capability in the user namespace governing that network namespace. If it succeeds, inspect the actual capability sets and namespace ownership; do not assume the test context was what you intended.

Cleanup interfaces:

```bash
sudo ip -n cap-net-lab link delete dummy0 2>/dev/null || true
sudo ip -n cap-net-lab link delete dummy1 2>/dev/null || true
```

**Investigate:** Why should the application not need `CAP_NET_ADMIN`? Which component configures the container network? Why is this capability dangerous when granted unnecessarily? How does a network namespace scope network administration?

## Exercise 5 — Understand CAP_SYS_ADMIN

`CAP_SYS_ADMIN` governs a large collection of privileged operations and is often described as broad or overloaded. Some mount and namespace operations require it, depending on the context and kernel version.

Read:

```bash
man 7 capabilities
```

Find `CAP_SYS_ADMIN` and record examples. Compare it with a narrower capability such as `CAP_NET_BIND_SERVICE` or `CAP_NET_RAW`.

Do not attempt arbitrary host mounts or kernel-administration operations.

**Investigate:** Why is a broad capability difficult to reason about? What is the security impact of granting it to a container? Why is `--privileged` much broader than adding one capability? What evidence should be collected before a capability exception is approved?

## Exercise 6 — File capabilities

File capabilities attach capability metadata to an executable. When executed, the metadata can affect the process's capability transition, subject to kernel rules, filesystem support, mount options and security policy.

Build a small program that attempts to bind a low TCP port:

```bash
cat > /tmp/cap-bind-test.c <<'EOF'
#include <arpa/inet.h>
#include <errno.h>
#include <stdio.h>
#include <string.h>
#include <sys/socket.h>
#include <unistd.h>

int main(void) {
    int fd = socket(AF_INET, SOCK_STREAM, 0);
    if (fd < 0) { perror("socket"); return 1; }

    struct sockaddr_in addr = {
        .sin_family = AF_INET,
        .sin_port = htons(80),
        .sin_addr.s_addr = htonl(INADDR_ANY)
    };

    if (bind(fd, (struct sockaddr *)&addr, sizeof(addr)) < 0) {
        fprintf(stderr, "bind failed: %s\n", strerror(errno));
        close(fd);
        return 2;
    }

    puts("Successfully bound TCP port 80");
    close(fd);
    return 0;
}
EOF

gcc -O2 -Wall -Wextra -o /tmp/cap-bind-test /tmp/cap-bind-test.c
```

Check the unprivileged port threshold:

```bash
sysctl net.ipv4.ip_unprivileged_port_start
```

If the threshold is 0, port 80 may be bindable without the capability. Record this; do not change the host sysctl just to force the demo.

Run as the test user:

```bash
sudo -u cap-lab /tmp/cap-bind-test
```

Apply the file capability:

```bash
sudo setcap cap_net_bind_service=ep /tmp/cap-bind-test
getcap /tmp/cap-bind-test
sudo -u cap-lab /tmp/cap-bind-test
```

Remove it:

```bash
sudo setcap -r /tmp/cap-bind-test
getcap /tmp/cap-bind-test || true
```

**Investigate:** What does `ep` mean? How do file capabilities affect execution? What if the binary is copied to a filesystem that does not preserve capabilities? Why must a file-capability-bearing executable be protected from modification? Is a file capability always safer than running the service as root?

Save threshold, output and `getcap` evidence.

## Exercise 7 — User namespaces and UID/GID mappings

A user namespace isolates user and group ID mappings and creates a capability context scoped to that namespace. A process can appear as UID 0 inside while mapping to an unprivileged UID on the host.

Inspect current mappings:

```bash
cat /proc/$$/uid_map
cat /proc/$$/gid_map
```

Create a user namespace:

```bash
unshare --user --map-root-user /bin/bash
```

Inside:

```bash
id
cat /proc/$$/uid_map
cat /proc/$$/gid_map
capsh --print
```

Compare with the host shell in another terminal, then exit.

Inspect subordinate ID ranges and helper tools:

```bash
cat /etc/subuid
cat /etc/subgid
command -v newuidmap
command -v newgidmap
```

Do not edit these mappings during this lab unless you understand existing allocations and have a rollback plan.

**Investigate:** What do the columns in `uid_map`/`gid_map` mean? Why is namespace root not host root? Why do rootless engines use subordinate UID/GID ranges? Which operations remain unavailable inside the namespace?

## Exercise 8 — Compare rootful and rootless containers

Use Docker or Podman and record versions.

Rootful Docker example:

```bash
docker version
docker info | grep -i -E 'rootless|cgroup|security'
docker run --rm debian:stable-slim sh -c '
  id
  grep "^Cap" /proc/self/status
  cat /proc/self/uid_map
  cat /proc/self/gid_map
'
```

If Podman is installed, inspect rootless mode:

```bash
podman info --debug | grep -i -E 'rootless|cgroup|graphroot'
podman run --rm debian:stable-slim sh -c '
  id
  grep "^Cap" /proc/self/status
  cat /proc/self/uid_map
  cat /proc/self/gid_map
'
```

Do not assume that running the Docker CLI as a non-root user means the daemon is rootless. Membership in the `docker` group on a rootful daemon can grant root-equivalent control of the host.

Compare actual observations:

| Area | Rootful engine | Rootless engine |
|---|---|---|
| Runtime privilege | Commonly privileged | Operates within user privileges/namespaces |
| UID mapping | Depends on config | User mapping is central |
| Networking | Host bridge integration commonly available | May use different user-space/network mechanisms |
| Storage | Engine-managed host paths | User-owned storage |
| Constraints | Broad host integration possible | Some kernel/network/storage features may be limited |

**Investigate:** Security advantage? Operational limitations? Why is Docker group membership sensitive? How does rootless networking differ? Which trade-offs matter in production?

## Exercise 9 — Harden a container

Run with non-root identity, all capabilities dropped, no-new-privileges, read-only rootfs, a writable temporary filesystem and resource limits:

```bash
docker run --rm \
  --user 65532:65532 \
  --cap-drop ALL \
  --security-opt no-new-privileges:true \
  --read-only \
  --tmpfs /tmp:rw,noexec,nosuid,size=16m \
  --pids-limit 64 \
  --memory 128m \
  --cpus 0.5 \
  debian:stable-slim \
  sh -c 'id; grep "^Cap" /proc/self/status; touch /tmp/test-file; touch /root/should-fail'
```

The `/tmp` write should work; a write to the read-only root or an unwritable path should fail. Numeric UID 65532 may not have a named account in the image; record that distinction.

For an inspectable process:

```bash
docker run -d --name cap-hardened \
  --user 65532:65532 --cap-drop ALL \
  --security-opt no-new-privileges:true \
  --read-only --tmpfs /tmp:rw,noexec,nosuid,size=16m \
  debian:stable-slim sleep 300

docker exec cap-hardened sh -c '
  id
  grep "^Cap" /proc/self/status
  grep NoNewPrivs /proc/self/status
  mount | head
'
docker rm -f cap-hardened
```

Add back a capability only when an application requirement justifies it. Example:

```bash
docker run --rm --cap-drop ALL --cap-add NET_BIND_SERVICE \
  --security-opt no-new-privileges:true \
  debian:stable-slim sh -c 'grep "^Cap" /proc/self/status'
```

**Investigate:** What changed with all capabilities dropped? What does `NoNewPrivs` show? Why use read-only rootfs and tmpfs? Why is `--privileged` not an acceptable general fix?

## 8. Failure injection

Perform at least four:

| Failure | Experiment | Expected observation |
|---|---|---|
| Missing capability | Drop `CAP_NET_RAW`, create raw socket | Permission denied |
| Missing network admin | Drop `CAP_NET_ADMIN`, configure test interface | Operation not permitted |
| Read-only rootfs | Write outside tmpfs | Read-only filesystem error |
| Non-root identity | Write to root-owned path | Permission denied |
| File capability absent | Run bind test without capability | Depends on port threshold; document |
| UID mapping | Inspect user namespace map | Namespace ID differs from host mapping |

For each, record identity, namespace, capability set, operation, error, relevant policy, root cause and least-privilege correction.

Do not automatically fix permission errors by adding capabilities. First decide whether the application should perform the operation.

## 9. Cleanup

```bash
sudo ip netns delete cap-net-lab 2>/dev/null || true
sudo setcap -r /tmp/cap-bind-test 2>/dev/null || true
sudo rm -f /tmp/cap-bind-test /tmp/cap-bind-test.c
docker rm -f cap-hardened 2>/dev/null || true
sudo userdel --remove cap-lab
```

Only remove resources created by this lab. Verify no test process or namespace remains.

## 10. Completion checklist

- [ ] Inspect all capability sets.
- [ ] Compare root and non-root.
- [ ] Drop a capability and demonstrate denial.
- [ ] Investigate `CAP_NET_ADMIN`.
- [ ] Explain `CAP_SYS_ADMIN` risk.
- [ ] Apply/remove a file capability.
- [ ] Create user namespace and inspect UID/GID mappings.
- [ ] Inspect subordinate UID/GID ranges.
- [ ] Compare rootful and rootless containers.
- [ ] Run a hardened container.
- [ ] Complete four failure investigations.
- [ ] Clean up users, namespaces, binaries and containers.

## 11. GitHub deliverables

```text
lab-04-linux-capabilities/
├── README.md
├── scripts/
│   ├── inspect-capabilities.sh
│   ├── test-raw-socket.py
│   ├── build-bind-test.sh
│   ├── inspect-userns.sh
│   └── cleanup.sh
├── manifests/
│   └── hardened-container.example.sh
├── diagrams/
│   └── capability-sets.mmd
├── evidence/
│   ├── current-shell-capabilities.txt
│   ├── root-vs-user.md
│   ├── capability-drop.md
│   ├── file-capabilities.md
│   ├── user-namespace-mapping.md
│   └── rootless-comparison.md
└── troubleshooting.md
```

Do not commit binaries, credentials or host-sensitive output. Scripts should identify commands requiring root and validate their target paths.

## 12. Blog article

**Suggested title:** Linux Capabilities and Container Security: Understanding Privilege Boundaries

Cover: UID 0 versus capabilities; five capability sets; inspecting processes; dropping `CAP_NET_RAW`; `CAP_NET_ADMIN` and `CAP_SYS_ADMIN`; file capabilities; user namespaces; rootful versus rootless; least-privilege container configuration; failure analysis.

### References

- [capabilities(7)](https://man7.org/linux/man-pages/man7/capabilities.7.html)
- [user_namespaces(7)](https://man7.org/linux/man-pages/man7/user_namespaces.7.html)
- [capsh(1)](https://man7.org/linux/man-pages/man1/capsh.1.html)
- [setcap(8)](https://man7.org/linux/man-pages/man8/setcap.8.html)
- [Docker runtime privilege and capabilities](https://docs.docker.com/engine/containers/run/)
- [Podman documentation](https://docs.podman.io/)
- [OCI Runtime Specification](https://github.com/opencontainers/runtime-spec)

## 13. Lab review

- **Capability investigation:** explain the five sets and operations tested.
- **User namespace findings:** explain mappings and namespace root.
- **Runtime comparison:** rootful/rootless observations and evidence.
- **Hardening decisions:** capabilities removed and why.
- **Failure investigations:** symptoms, evidence and root causes.
- **Remaining questions:** topics to revisit during the container security week.
