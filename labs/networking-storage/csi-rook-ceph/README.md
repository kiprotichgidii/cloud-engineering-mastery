# Week 12: CSI, distributed storage and stateful workloads

[← Back to Month 3: Cloud-Native Networking and Storage](../README.md)

## Weekly Overview
**Goal:** Deep technical investigation and hands-on laboratory exercises for Week 12.

## Concepts to Investigate
- CSI controller and node services.
- Volume provisioning, attach, mount and expansion.
- StorageClasses and reclaim policies.
- Rook-Ceph architecture.
- Replication, quorum and failure domains.
- StatefulSets and application consistency.
- Snapshots, backups and restore.
- Storage performance and latency.

## Hands-on Laboratory Modules
1. [**Lab 01 Pvc Tracing**](lab-01-pvc-tracing/README.md)
2. [**Lab 02 Rook Ceph**](lab-02-rook-ceph/README.md)
3. [**Lab 03 Storage Failure Recovery**](lab-03-storage-failure-recovery/README.md)
4. [**Lab 04 Storage Benchmarking**](lab-04-storage-benchmarking/README.md)

### Detailed Curriculum Specifications
1. Trace a PVC from claim creation through provisioning and mount.
2. Deploy or extend a Rook-Ceph test cluster and inspect Ceph health, MON, MGR and OSD behavior.
3. Simulate a storage component or node failure and observe degraded operation and recovery.
4. Benchmark persistent storage using a controlled workload; compare latency and throughput under different conditions.
5. Test snapshot and restore for a stateful application; verify application-level data consistency.

##
