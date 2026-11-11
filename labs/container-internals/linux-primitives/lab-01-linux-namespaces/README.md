# Lab: Build a container-like environment using unshare

[← Back to Week 1: Linux kernel primitives behind containers](../README.md) | [Month 1: Container Internals and Runtime Engineering](../../README.md)

## Objective
Isolate UTS, PID, mount, IPC and network namespaces with unshare and nsenter

## Research questions
- What do I expect to discover?
- Which component or kernel/runtime boundary am I investigating?
- What are the operational limits and failure modes?

## Prerequisites
- OS and kernel version: Linux 6.x (Ubuntu 24.04 LTS / Debian 12 recommended)
- Required tools: Standard toolset pinned per module
- Infrastructure: Dedicated homelab VM or isolated sandbox node

## Architecture
- Namespace architecture diagram: [namespace-architecture.mmd](diagrams/namespace-architecture.mmd)
- Veth network diagram: [veth-network.mmd](diagrams/veth-network.mmd)

## Implementation & Lab Instructions
Refer to the weekly curriculum specification:

**Lab 1.1 — Build a container-like environment using `unshare`**
- Create isolated UTS, PID, mount, IPC and network namespaces.
- Use `unshare` to start a shell in the new namespaces.
- Mount a separate `/proc` and inspect the process tree.
- Compare namespace inode identifiers between host and isolated processes.
- Enter and inspect namespaces with `nsenter`.
- Document which resources are isolated and which remain shared.

## Execution Scripts
- Create network namespaces: [`scripts/create-network-namespaces.sh`](scripts/create-network-namespaces.sh)
- Test connectivity: [`scripts/test-connectivity.sh`](scripts/test-connectivity.sh)
- Teardown & cleanup: [`scripts/cleanup.sh`](scripts/cleanup.sh)

## Failure Injection & Troubleshooting
- Failure injection plan: What can be deliberately broken? What symptoms are observed?
- Root cause analysis and resolution: [troubleshooting.md](troubleshooting.md)

## Evidence to Collect
- Namespace identifiers: [`evidence/namespace-identifiers.txt`](evidence/namespace-identifiers.txt)
- PID isolation: [`evidence/pid-isolation.md`](evidence/pid-isolation.md)
- Mount isolation: [`evidence/mount-isolation.md`](evidence/mount-isolation.md)
- Network connectivity: [`evidence/network-connectivity.md`](evidence/network-connectivity.md)
- Packet capture: [`evidence/packet-capture.md`](evidence/packet-capture.md)

## Technical Article Reference
**“Linux Containers Under the Hood: Namespaces, cgroups and the Kernel Primitives That Make Isolation Possible”**

## Exit Criteria
Explain how namespaces isolate views of system resources, how cgroups account for and limit resources, and why neither mechanism alone provides a complete security boundary.
