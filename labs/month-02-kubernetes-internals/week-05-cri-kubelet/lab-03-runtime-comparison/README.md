# Lab: Containerd vs CRI-O comparison

[← Back to Week 5: CRI, kubelet and container lifecycle](../README.md) | [Month 2: Container Orchestration and Kubernetes Internals](../../README.md)

## Objective
Compare node runtime configurations, shim lifecycles, and CRI endpoints

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

3. **Runtime comparison:** Configure a disposable cluster with containerd and investigate CRI-O as a comparison environment.

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
**“How Kubernetes Runs Containers: From Pod Creation to the OCI Runtime”**

## Exit Criteria
Trace a Pod from the Kubernetes API to the process running on a node and identify which component owns each stage.
