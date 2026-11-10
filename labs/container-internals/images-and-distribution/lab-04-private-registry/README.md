# Lab: Operate an OCI registry

[← Back to Week 3: Image construction, storage and distribution](../README.md) | [Month 1: Container Internals and Runtime Engineering](../../README.md)

## Objective
Deploy an authenticated private OCI registry, push/pull by digest, and test retention

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

**Lab 3.4 — Operate an OCI registry**
- Deploy a private registry.
- Push, pull and inspect images.
- Configure authentication and TLS.
- Test digest-based deployment.
- Explore retention and garbage collection.
- Simulate registry unavailability and observe the effect on new and already-running workloads.

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
**“OCI Images Explained: Layers, Digests, OverlayFS and BuildKit in Practice”**

## Exit Criteria
Explain image metadata, content addressing, layer reuse, writable snapshots, registry operations and reproducible image builds.
