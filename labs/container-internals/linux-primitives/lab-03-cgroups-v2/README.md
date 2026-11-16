# Lab 1.3 — Linux cgroups v2: Resource Control and Accounting

> **Module:** Month 1 — Container Internals and Runtime Engineering  
> **Week:** 1 — Linux Kernel Primitives  
> **Estimated effort:** 5–7 hours | **Difficulty:** Intermediate / advanced  
> **Environment:** Disposable Ubuntu Server 24.04 or Debian 13 VM on Proxmox  
> **Tools:** cgroups v2, systemd, `stress-ng`, `ps`, `vmstat`, `findmnt`

## 1. Objective

Investigate how Linux accounts for and constrains CPU, memory and process consumption. Create a safe test cgroup, place workloads inside it, apply resource limits, generate controlled pressure and inspect kernel accounting and event files.

By the end, explain how container runtimes configure cgroups, how CPU throttling differs from CPU usage, how memory limits interact with OOM handling, and how Kubernetes resource settings relate to kernel controls.

## 2. Engineering context

Namespaces control what a process can see. They do not stop a process from consuming all available CPU or memory.

On a shared container host, one workload with a memory leak can affect unrelated applications. A CPU-intensive workload can consume scheduler time needed by latency-sensitive services. Cgroups provide kernel-level resource accounting and controls.

Key distinctions:

- **CPU quota** limits CPU time over a period and can throttle execution.
- **CPU weight** determines relative share among competing sibling cgroups; it is not an absolute quota.
- **Memory high** is a pressure/reclaim threshold.
- **Memory max** is a hard limit that can lead to reclaim and OOM handling.
- **PID max** limits tasks/processes that can be created in a cgroup.
- **Kubernetes requests** primarily influence scheduling; **limits** configure enforcement for supported resources.

Exact behaviour depends on kernel, cgroup mode, systemd, runtime and Kubernetes configuration.

## 3. Safety

This lab deliberately creates resource pressure. Use only the disposable VM and take a Proxmox snapshot.

- Never change limits on the root cgroup.
- Use a dedicated delegated subtree.
- Do not place systemd or SSH service processes in the test cgroup.
- Start with conservative limits and short workloads.
- Keep the Proxmox console available.
- If the VM becomes unstable, stop the lab workload from the hypervisor console.
- Do not blindly write to `/sys/fs/cgroup`; understand ownership and delegation first.

## 4. Preparation and cgroup v2 verification

Install:

```bash
sudo apt update
sudo apt install -y stress-ng sysstat procps util-linux
```

Record environment:

```bash
uname -a
cat /etc/os-release
systemd --version | head -1
```

Check the cgroup filesystem:

```bash
stat -fc %T /sys/fs/cgroup
findmnt -t cgroup2
cat /proc/$$/cgroup
```

For a unified v2 hierarchy, `stat` should report `cgroup2fs`, and `/proc/<pid>/cgroup` commonly has a line such as `0::/some/path`.

Inspect controllers:

```bash
cat /sys/fs/cgroup/cgroup.controllers
cat /sys/fs/cgroup/cgroup.subtree_control
```

Save output to `evidence/cgroup-baseline.txt`.

**Investigate:** What is a unified hierarchy? How does it differ from the multiple hierarchies common in cgroups v1? Which controllers are available? Why might a controller be listed but not enabled for child cgroups?

## 5. Controller interfaces

| File | Purpose |
|---|---|
| `cpu.max` | CPU bandwidth quota and period |
| `cpu.weight` | Relative CPU weight |
| `cpu.stat` | CPU usage and throttling statistics |
| `memory.max` | Hard memory limit |
| `memory.high` | Memory pressure/reclaim threshold |
| `memory.current` | Current memory usage |
| `memory.events` | High/max/OOM event counters |
| `memory.stat` | Detailed memory accounting |
| `memory.pressure` | Memory pressure stall information, if enabled |
| `pids.max` | Maximum number of tasks |
| `pids.current` | Current task count |
| `cgroup.procs` | Process IDs in cgroup |
| `cgroup.controllers` | Available controllers |
| `cgroup.subtree_control` | Controllers enabled for children |

Files can be absent if a controller is unavailable or not enabled in the relevant subtree.

---

## Exercise 1 — Understand the hierarchy

Inspect:

```bash
sudo ls -la /sys/fs/cgroup
systemd-cgls
cat /proc/$$/cgroup
```

Map the current shell's path:

```bash
CGROUP_PATH=$(awk -F: '$1 == "0" {print $3}' /proc/$$/cgroup)
printf 'Current cgroup path: %s\n' "$CGROUP_PATH"
```

Inspect the corresponding directory:

```bash
CGROUP_DIR="/sys/fs/cgroup${CGROUP_PATH}"
ls -ld "$CGROUP_DIR"
```

**Investigate:** Which cgroup owns your shell? How does systemd organize sessions and services? What is the difference between a controller being available and being enabled for descendants?

## Exercise 2 — Create a safe delegated test cgroup

On systemd-managed hosts, systemd normally manages the hierarchy. Directly changing the root hierarchy can conflict with systemd. Use a delegated scope if supported.

Start a transient scope:

```bash
sudo systemd-run --scope --unit=cgroup-lab.scope \
  -p Delegate=yes /bin/bash
```

Keep this shell open. Inside it:

```bash
cat /proc/$$/cgroup
CGROUP_PATH=$(awk -F: '$1 == "0" {print $3}' /proc/$$/cgroup)
CGROUP_DIR="/sys/fs/cgroup${CGROUP_PATH}"
printf '%s\n' "$CGROUP_DIR"
ls -ld "$CGROUP_DIR"
cat "$CGROUP_DIR/cgroup.controllers"
cat "$CGROUP_DIR/cgroup.subtree_control"
```

If the directory is not writable or delegation did not expose the required controllers, stop and investigate your distribution's systemd delegation configuration. Do not bypass the issue by writing to the root cgroup.

Enable only controllers listed as available. For example, if `cpu`, `memory` and `pids` are present:

```bash
printf '+cpu +memory +pids\n' | sudo tee "$CGROUP_DIR/cgroup.subtree_control"
```

Depending on the delegation setup, the delegated user may write without sudo, or systemd may reject the operation. Follow the actual ownership and permission model.

Create a child:

```bash
sudo mkdir "$CGROUP_DIR/workload"
cat "$CGROUP_DIR/workload/cgroup.controllers"
```

If controller files are not present, inspect parent enablement and cgroup v2's no-internal-process and subtree rules. Record the exact error rather than guessing.

## Exercise 3 — CPU quota, weight and throttling

`cpu.max` contains `MAX PERIOD` in microseconds. For example, `50000 100000` permits 50 ms of CPU time in each 100 ms period—approximately half of one CPU over time.

Inspect and configure:

```bash
cat "$CGROUP_DIR/workload/cpu.max"
echo "50000 100000" | sudo tee "$CGROUP_DIR/workload/cpu.max"
echo 100 | sudo tee "$CGROUP_DIR/workload/cpu.weight"
```

Run a short workload inside the target cgroup. The shell moves itself into the cgroup and then replaces itself with stress-ng:

```bash
sudo bash -c 'echo $$ > "$1/cgroup.procs"; exec stress-ng --cpu 2 --timeout 30s --metrics-brief' \
  _ "$CGROUP_DIR/workload"
```

Inspect after the run:

```bash
cat "$CGROUP_DIR/workload/cpu.stat"
cat "$CGROUP_DIR/workload/cpu.max"
```

Repeat in a fresh test cgroup or after restoring the original quota, then compare.

Common `cpu.stat` fields include `usage_usec`, `user_usec`, `system_usec`, `nr_periods`, `nr_throttled` and `throttled_usec`; exact fields vary by kernel.

**Investigate:**
1. How does throughput change with a 0.5 CPU quota?
2. Does the limit stop execution or throttle it?
3. What does `nr_throttled` tell you?
4. Why can usage be high while throttling also occurs?
5. How is `cpu.weight` different from `cpu.max`?
6. What could throttling mean for a latency-sensitive service?

Save benchmark output and before/after `cpu.stat`.

## Exercise 4 — Memory high, max and OOM events

Inspect current values:

```bash
cat "$CGROUP_DIR/workload/memory.current"
cat "$CGROUP_DIR/workload/memory.max"
cat "$CGROUP_DIR/workload/memory.events"
cat "$CGROUP_DIR/workload/memory.stat" | head -30
```

On an 8 GB VM, a 512 MiB test limit can be a reasonable starting point if there is sufficient headroom:

```bash
echo $((512 * 1024 * 1024)) | sudo tee "$CGROUP_DIR/workload/memory.max"
echo $((384 * 1024 * 1024)) | sudo tee "$CGROUP_DIR/workload/memory.high"
```

Run a workload below the hard limit:

```bash
sudo bash -c 'echo $$ > "$1/cgroup.procs"; exec stress-ng --vm 1 --vm-bytes 256M --vm-keep --timeout 20s --metrics-brief' \
  _ "$CGROUP_DIR/workload"
```

Observe in another terminal:

```bash
watch -n 1 "cat '$CGROUP_DIR/workload/memory.current'; cat '$CGROUP_DIR/workload/memory.events'"
```

If the VM remains healthy and has adequate free memory, run a controlled over-limit attempt:

```bash
sudo bash -c 'echo $$ > "$1/cgroup.procs"; exec stress-ng --vm 1 --vm-bytes 768M --vm-keep --timeout 20s --metrics-brief' \
  _ "$CGROUP_DIR/workload"
```

The workload may experience allocation failures, reclaim or cgroup OOM handling. Observe rather than assume a particular message.

Afterwards:

```bash
cat "$CGROUP_DIR/workload/memory.current"
cat "$CGROUP_DIR/workload/memory.events"
dmesg | tail -80
```

`dmesg` access may be restricted; use permitted system logs if so.

**Investigate:** What changed in `memory.current`? Which counters incremented? How do `memory.high` and `memory.max` differ? How does a cgroup OOM event differ from a host-wide OOM event? How can page cache affect observed memory?

Do not repeatedly stress memory until the VM becomes unstable.

## Exercise 5 — PID controller

Inspect:

```bash
cat "$CGROUP_DIR/workload/pids.current"
cat "$CGROUP_DIR/workload/pids.max"
```

Set a conservative limit:

```bash
echo 64 | sudo tee "$CGROUP_DIR/workload/pids.max"
```

Run a short process-creation workload:

```bash
sudo bash -c 'echo $$ > "$1/cgroup.procs"; exec stress-ng --fork 128 --timeout 15s --metrics-brief' \
  _ "$CGROUP_DIR/workload"
```

Inspect:

```bash
cat "$CGROUP_DIR/workload/pids.current"
cat "$CGROUP_DIR/workload/pids.max"
```

**Investigate:** What is counted by `pids.current`? What happens when a process tries to exceed the limit? Why is a PID limit useful for multi-tenant systems? How does this relate to Kubernetes node-level Pod PID limits?

## Exercise 6 — Events and pressure stall information

Inspect:

```bash
cat "$CGROUP_DIR/workload/cgroup.events"
cat "$CGROUP_DIR/workload/memory.events"
cat "$CGROUP_DIR/workload/cpu.stat"
```

If available:

```bash
cat "$CGROUP_DIR/workload/memory.pressure"
cat "$CGROUP_DIR/workload/cpu.pressure"
```

Pressure Stall Information (PSI) describes time tasks are stalled due to resource contention. It can help distinguish simple resource utilization from resource pressure that affects progress.

**Investigate:** What is the difference between usage and pressure? Why might PSI be useful for capacity planning? Which CPU and memory signals would you alert on? Why should alerts focus on workload impact rather than one raw counter?

Populate this table using measured values:

| Experiment | Limit | Workload | Measured result |
|---|---:|---|---|
| CPU | 0.5 CPU | 2 workers | Throughput and throttling |
| Memory | 512 MiB | 256 MiB allocation | Peak usage/events |
| Memory pressure | 384 MiB high | 768 MiB attempted | Reclaim/OOM observations |
| PID | 64 tasks | Fork workload | Peak count/error |

## Exercise 7 — Compare with Docker or Podman

Example with Docker:

```bash
docker run --rm \
  --cpus=0.5 --memory=512m --pids-limit=64 \
  debian:stable-slim \
  sh -c 'cat /sys/fs/cgroup/cpu.max; cat /sys/fs/cgroup/memory.max; cat /sys/fs/cgroup/pids.max'
```

For an inspectable workload:

```bash
docker run -d --name cgroup-reference \
  --cpus=0.5 --memory=512m --pids-limit=64 \
  debian:stable-slim sleep 300
docker inspect cgroup-reference
docker exec cgroup-reference sh -c 'cat /proc/self/cgroup'
```

Inspect the host-side process and cgroup, then remove it:

```bash
docker rm -f cgroup-reference
```

Repeat with Podman if available. Record actual engine, version, cgroup driver and effective values. Do not assume the displayed values will have the same units as CLI flags.

**Investigate:** Which cgroup files changed? Which component writes them? How does the runtime select a cgroup? What role does systemd's cgroup driver play?

## 8. Kubernetes connection

| Kubernetes field | Typical relationship |
|---|---|
| `requests.cpu` | Scheduling input; may influence relative CPU weight |
| `limits.cpu` | CPU quota/throttling configuration |
| `requests.memory` | Scheduling input, not itself a hard memory cap |
| `limits.memory` | Memory cgroup limit |
| Pod PID limit | Runtime/kubelet/node-level process limit |

This is a conceptual mapping. Exact implementation depends on Kubernetes version, feature gates, QoS class, kubelet settings, cgroup version and runtime.

In a later lab, inspect a real Pod's cgroup and compare its files with its Pod specification.

## 9. Failure injection

Complete at least four:

| Failure | Experiment | Symptom | Evidence |
|---|---|---|---|
| CPU throttling | Set low quota | Reduced throughput and throttling counters | `cpu.stat`, benchmark |
| Memory limit | Allocate above max | Reclaim, allocation failure or cgroup OOM | `memory.events`, logs |
| PID exhaustion | Set low max | Task creation denied | `pids.current`, error |
| Controller unavailable | Enable unavailable controller | Kernel rejects write | parent controller files |
| Delegation denied | Write outside delegated subtree | Permission denied | ownership/path |
| Busy cgroup | Remove while populated | Removal fails | `cgroup.procs` |

Record change, expected and actual behaviour, evidence, root cause and recovery.

## 10. Cleanup

Stop only lab stress processes. Inspect:

```bash
pgrep -a stress-ng || true
cat "$CGROUP_DIR/workload/cgroup.procs"
```

Terminate lab processes cleanly. Remove the child cgroup only when empty:

```bash
sudo rmdir "$CGROUP_DIR/workload"
```

Exit the delegated scope shell. Inspect:

```bash
systemd-cgls
```

Stop the transient scope if it remains:

```bash
sudo systemctl stop cgroup-lab.scope
```

Verify no test workload remains.

## 11. Completion checklist

- [ ] Verify cgroups v2 and record controllers.
- [ ] Explain hierarchy and systemd delegation.
- [ ] Create a safe delegated child cgroup.
- [ ] Apply CPU quota and observe throttling.
- [ ] Apply memory controls and observe events.
- [ ] Apply PID limit and observe task creation.
- [ ] Inspect PSI where available.
- [ ] Compare direct controls with Docker/Podman.
- [ ] Explain Kubernetes requests versus limits.
- [ ] Complete four failure investigations.
- [ ] Clean up all test processes and cgroups.

## 12. GitHub deliverables

```text
lab-03-cgroups-v2/
├── README.md
├── scripts/
│   ├── inspect-cgroups.sh
│   ├── create-test-cgroup.sh
│   ├── run-cpu-test.sh
│   ├── run-memory-test.sh
│   ├── run-pid-test.sh
│   └── cleanup.sh
├── diagrams/
│   └── cgroups-v2-hierarchy.mmd
├── evidence/
│   ├── cgroup-baseline.txt
│   ├── cpu-experiment.md
│   ├── memory-experiment.md
│   ├── pid-experiment.md
│   └── runtime-comparison.md
└── troubleshooting.md
```

Scripts must verify the target is the intended delegated subtree and refuse to write to the cgroup root or an unexpected path.

## 13. Blog article

**Suggested title:** Linux cgroups v2 Explained: CPU, Memory and Process Limits for Containers

Cover: why namespaces are not resource controls; v1 versus v2; hierarchy and delegation; CPU quota/weight/throttling; memory high/max/OOM; PID limits; PSI; runtime flags; Kubernetes resource requests and limits; failure experiments.

### References

- [Linux cgroup v2 documentation](https://docs.kernel.org/admin-guide/cgroup-v2.html)
- [systemd Control Group Interface](https://systemd.io/CONTROL_GROUP_INTERFACE/)
- [systemd.resource-control](https://www.freedesktop.org/software/systemd/man/latest/systemd.resource-control.html)
- [Linux PSI documentation](https://docs.kernel.org/accounting/psi.html)
- [stress-ng](https://github.com/ColinIanKing/stress-ng)
- [Kubernetes resource management](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/)

## 14. Lab review

- **Environment:** kernel, cgroup version, controllers and delegation model.
- **Measurements:** CPU, memory and PID results.
- **Failure investigations:** symptoms, evidence and root causes.
- **Runtime comparison:** how Docker/Podman settings appeared in cgroup files.
- **Remaining questions:** topics to revisit in Kubernetes resource and scheduling labs.
