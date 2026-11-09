# Lab: Enforce resource limits with cgroups v2

[← Back to Week 1: Linux kernel primitives behind containers](../README.md) | [Month 1: Container Internals and Runtime Engineering](../../README.md)

## Objective
Enforce CPU, memory, and PID limits using unified cgroups v2 controllers

## Research questions
- What do I expect to discover?
- Which component or kernel/runtime boundary am I investigating?
- What are the operational limits and failure modes?

## Prerequisites
- OS and kernel version: Linux 6.x (Ubuntu 24.04 LTS / Debian 12 recommended)
- Required tools: Standard toolset pinned per module
- Infrastructure: Dedicated homelab VM or isolated sandbox node

## Architecture
- Architecture diagram: [diagram.mmd](architecture/diagram.mmd)
- Component manifests: [`manifests/`](manifests/)

## Implementation & Lab Instructions
Refer to the weekly curriculum specification:

**Lab 1.3 — Enforce resource limits with cgroups v2**
- Create a cgroup and move a test process into it.
- Configure CPU, memory and PID limits.
- Generate CPU and memory pressure.
- Inspect `cpu.stat`, `memory.current`, `memory.events` and `pids.current`.
- Observe throttling and OOM behavior.
- Compare host-level and cgroup-level measurements.

## Execution Scripts
- Setup environment: [`scripts/setup.sh`](scripts/setup.sh)
- Validate implementation: [`scripts/validate.sh`](scripts/validate.sh)
- Teardown & cleanup: [`scripts/cleanup.sh`](scripts/cleanup.sh)

## Failure Injection & Troubleshooting
- Failure injection plan: What can be deliberately broken? What symptoms are observed?
- Root cause analysis and resolution: [troubleshooting.md](troubleshooting.md)

## Evidence to Collect
- Terminal output and logs: [`evidence/command-output.md`](evidence/command-output.md)
- Metric observations and traces: [`evidence/observations.md`](evidence/observations.md)

## Technical Article Reference
**“Linux Containers Under the Hood: Namespaces, cgroups and the Kernel Primitives That Make Isolation Possible”**

## Exit Criteria
Explain how namespaces isolate views of system resources, how cgroups account for and limit resources, and why neither mechanism alone provides a complete security boundary.
