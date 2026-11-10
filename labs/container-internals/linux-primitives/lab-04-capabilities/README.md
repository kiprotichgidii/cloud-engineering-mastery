# Lab: Linux capabilities and privilege boundaries

[← Back to Week 1: Linux kernel primitives behind containers](../README.md) | [Month 1: Container Internals and Runtime Engineering](../../README.md)

## Objective
Inspect capabilities with capsh and build least-privilege profiles

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

**Lab 1.4 — Linux capabilities and privilege boundaries**
- Inspect capabilities using `capsh` and `/proc`.
- Run processes with reduced capabilities.
- Test operations that require capabilities such as `NET_ADMIN` and `SYS_ADMIN`.
- Demonstrate why privileged containers are dangerous.
- Build a least-privilege capability profile for a sample service.

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
