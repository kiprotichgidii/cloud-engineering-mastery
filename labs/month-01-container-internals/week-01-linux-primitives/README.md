# Week 1: Linux kernel primitives behind containers

[← Back to Month 1: Container Internals and Runtime Engineering](../README.md)

## Weekly Overview
**Goal:** Deep technical investigation and hands-on laboratory exercises for Week 1.

## Concepts to Investigate
- Process model, PID namespaces and process trees.
- Mount, UTS, IPC, network and user namespaces.
- Namespace membership and `/proc/<pid>/ns`.
- cgroups v1 versus unified cgroups v2.
- Resource controllers: CPU, memory, pids and I/O.
- Linux capabilities and privilege boundaries.
- `chroot` versus `pivot_root`.
- Mount propagation and filesystem isolation.

## Hands-on Laboratory Modules
1. [**Lab 01 Unshare Namespaces**](lab-01-unshare-namespaces/README.md)
2. [**Lab 02 Minimal Rootfs**](lab-02-minimal-rootfs/README.md)
3. [**Lab 03 Cgroups V2**](lab-03-cgroups-v2/README.md)
4. [**Lab 04 Capabilities**](lab-04-capabilities/README.md)

### Detailed Curriculum Specifications
**Lab 1.1 — Build a container-like environment using `unshare`**
- Create isolated UTS, PID, mount, IPC and network namespaces.
- Use `unshare` to start a shell in the new namespaces.
- Mount a separate `/proc` and inspect the process tree.
- Compare namespace inode identifiers between host and isolated processes.
- Enter and inspect namespaces with `nsenter`.
- Document which resources are isolated and which remain shared.

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
**“Linux Containers Under the Hood: Namespaces, cgroups and the Kernel Primitives That Make Isolation Possible”**

## Exit Criteria
Explain how namespaces isolate views of system resources, how cgroups account for and limit resources, and why neither mechanism alone provides a complete security boundary.
