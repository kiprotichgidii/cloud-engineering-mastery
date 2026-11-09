# Lab: Custom controller development with client-go

[← Back to Week 6: Control plane, API server, etcd and controllers](../README.md) | [Month 2: Container Orchestration and Kubernetes Internals](../../README.md)

## Objective
Build and test a simple client-go reconciliation controller watching custom events

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

4. **Controller development:** Build a small client-go controller that watches a resource and reconciles a simple desired state. Document retries, idempotency and error handling.

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
**“Kubernetes Reconciliation Explained: API Server, etcd, Watches and Controllers”**

## Exit Criteria
Explain declarative control loops and demonstrate a small controller that safely reconciles desired state.
