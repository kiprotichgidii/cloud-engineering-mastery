# Lab: Build a hardened container profile

[← Back to Week 4: Container security, isolation and runtime hardening](../README.md) | [Month 1: Container Internals and Runtime Engineering](../../README.md)

## Objective
Implement non-root user, dropped capabilities, no-new-privileges, and read-only rootfs

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

**Lab 4.1 — Build a hardened container profile**
- Run an application as a non-root user.
- Drop all unnecessary capabilities.
- Enable `no-new-privileges`.
- Apply a read-only root filesystem.
- Use a temporary writable mount only where needed.
- Set CPU, memory and PID limits.
- Verify each control from inside and outside the container.

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


## Exit Criteria
