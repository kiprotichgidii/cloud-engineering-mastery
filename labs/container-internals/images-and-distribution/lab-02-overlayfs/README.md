# Lab: Inspect OverlayFS and container writable layers

[← Back to Week 3: Image construction, storage and distribution](../README.md) | [Month 1: Container Internals and Runtime Engineering](../../README.md)

## Objective
Explore lowerdir, upperdir, merged, and workdir in OverlayFS with container changes

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

**Lab 3.2 — Inspect OverlayFS and container writable layers**
- Run a container and locate its snapshot or storage directories.
- Inspect OverlayFS mount options.
- Modify files inside the container and identify where changes appear.
- Compare a writable container with a read-only image layer.
- Remove the container and observe the lifecycle of writable data.
- Explain why persistent application data should not depend on the writable layer.

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
