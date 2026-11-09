# Lab: Inspect and execute an OCI bundle

[← Back to Week 2: Container runtime architecture and OCI](../README.md) | [Month 1: Container Internals and Runtime Engineering](../../README.md)

## Objective
Create and execute an OCI bundle directly using runc with custom config.json

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

**Lab 2.3 — Inspect and execute an OCI bundle**
- Create an OCI bundle using a runtime tooling workflow.
- Inspect `config.json`, root filesystem and process configuration.
- Run the bundle using runc.
- Change namespace, mount, capability and resource settings.
- Observe the effect of each change.
- Cleanly delete the runtime state.

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
**“From Docker CLI to runc: Following the Lifecycle of a Container”**

## Exit Criteria
Explain the responsibilities of Docker Engine, containerd, CRI, OCI runtimes and shim processes, and demonstrate running a workload without relying on Docker.
