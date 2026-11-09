# Week 8: Kubernetes operations, upgrades and recovery

[← Back to Month 2: Container Orchestration and Kubernetes Internals](../README.md)

## Weekly Overview
**Goal:** Deep technical investigation and hands-on laboratory exercises for Week 8.

## Concepts to Investigate
- kubeadm lifecycle.
- Certificate management and rotation.
- Control-plane and node upgrades.
- etcd backup and restore.
- Node drain and disruption.
- Static Pod recovery.
- Version skew and API deprecation.
- Cluster troubleshooting methodology.

## Hands-on Laboratory Modules
1. [**Lab 01 Cluster Upgrades**](lab-01-cluster-upgrades/README.md)
2. [**Lab 02 Node Failures**](lab-02-node-failures/README.md)
3. [**Lab 03 Control Plane Recovery**](lab-03-control-plane-recovery/README.md)
4. [**Lab 04 Certificate Maintenance**](lab-04-certificate-maintenance/README.md)

### Detailed Curriculum Specifications
1. **Cluster upgrade rehearsal:** Upgrade a disposable kubeadm cluster one supported version step at a time.
2. **Node failure exercise:** Drain, stop and recover a worker; observe workload rescheduling and disruption.
3. **Control-plane recovery:** Simulate a recoverable control-plane or etcd failure and follow a documented recovery procedure.
4. **Certificate and API maintenance:** Inspect certificate expiry and deprecated API usage; rehearse safe maintenance.

##
