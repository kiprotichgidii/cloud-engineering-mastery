# Week 5: CRI, kubelet and container lifecycle

[← Back to Month 2: Container Orchestration and Kubernetes Internals](../README.md)

## Weekly Overview
**Goal:** Deep technical investigation and hands-on laboratory exercises for Week 5.

## Concepts to Investigate
- Kubernetes CRI architecture.
- Kubelet responsibilities.
- CRI-O versus containerd integration.
- Pod sandbox and pause containers.
- Image service and runtime service.
- Pod lifecycle and termination.
- Probes, restart behavior and graceful shutdown.
- Static Pods and node-level components.

## Hands-on Laboratory Modules
1. [**Lab 01 Cri Inspection**](lab-01-cri-inspection/README.md)
2. [**Lab 02 Pod Sandbox Investigation**](lab-02-pod-sandbox-investigation/README.md)
3. [**Lab 03 Runtime Comparison**](lab-03-runtime-comparison/README.md)
4. [**Lab 04 Lifecycle Failures**](lab-04-lifecycle-failures/README.md)

### Detailed Curriculum Specifications
1. **CRI inspection:** Use `crictl` to inspect runtime information, images, Pods and containers directly on a Kubernetes node.
2. **Pod sandbox investigation:** Trace Pod creation from API request through kubelet, CRI, containerd and OCI runtime.
3. **Runtime comparison:** Configure a disposable cluster with containerd and investigate CRI-O as a comparison environment.
4. **Lifecycle failure lab:** Reproduce image-pull, sandbox creation, startup, probe and termination failures; diagnose each from node and control-plane evidence.

## Evidence to Commit
- Pod creation sequence diagram.
- CRI inspection scripts.
- Runtime logs and failure analysis.
- Container lifecycle troubleshooting runbook.

## Blog Article Target
**“How Kubernetes Runs Containers: From Pod Creation to the OCI Runtime”**

## Exit Criteria
Trace a Pod from the Kubernetes API to the process running on a node and identify which component owns each stage.
