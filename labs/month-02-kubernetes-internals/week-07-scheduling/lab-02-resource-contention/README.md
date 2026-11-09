# Lab: Resource requests, limits & QoS contention

[← Back to Week 7: Scheduling, resources and workload placement](../README.md) | [Month 2: Container Orchestration and Kubernetes Internals](../../README.md)

## Objective
Induce CPU throttling and memory OOM events across Guaranteed, Burstable, BestEffort QoS

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

2. **Resource contention lab:** Generate CPU and memory pressure; inspect throttling, OOM events and node conditions.

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
**“Inside the Kubernetes Scheduler: Resource Requests, Constraints and Placement Decisions”**

## Exit Criteria
Diagnose why a Pod is unschedulable and explain the difference between resource requests, limits, application scaling and node scaling.
