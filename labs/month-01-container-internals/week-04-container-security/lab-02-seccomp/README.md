# Lab: Develop and test a seccomp profile

[← Back to Week 4: Container security, isolation and runtime hardening](../README.md) | [Month 1: Container Internals and Runtime Engineering](../../README.md)

## Objective
Trace system calls and author a restrictive seccomp profile with audit verification

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

**Lab 4.2 — Develop and test a seccomp profile**
- Trace the syscalls used by a sample application.
- Create a restrictive seccomp profile.
- Run the application with the profile.
- Introduce a required syscall that is denied.
- Inspect the failure and kernel audit/log evidence.
- Refine the profile without blindly allowing every syscall.

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
