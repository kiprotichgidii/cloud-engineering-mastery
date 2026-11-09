# Week 20: Multi-tenancy, isolation and cloud cost engineering

[← Back to Month 5: Cloud-Native Security and Cloud Infrastructure](../README.md)

## Weekly Overview
**Goal:** Deep technical investigation and hands-on laboratory exercises for Week 20.

## Concepts to Investigate
- Namespace-based tenancy and its limitations.
- RBAC boundaries.
- Network isolation.
- Resource quotas and limit ranges.
- Dedicated nodes and taints.
- Runtime isolation options such as gVisor and Kata Containers.
- Node and workload density.
- Requests versus actual utilization.
- Cost allocation and chargeback concepts.

## Hands-on Laboratory Modules
1. [**Lab 01 Multi Tenancy Design**](lab-01-multi-tenancy-design/README.md)
2. [**Lab 02 Cross Tenant Isolation**](lab-02-cross-tenant-isolation/README.md)
3. [**Lab 03 Sandboxed Runtimes**](lab-03-sandboxed-runtimes/README.md)
4. [**Lab 04 Runtime Benchmarking**](lab-04-runtime-benchmarking/README.md)

### Detailed Curriculum Specifications
1. Design two tenant environments with separate identities, quotas, policies and network boundaries.
2. Attempt controlled cross-tenant access and verify that the controls prevent it.
3. Compare standard containers with an alternative sandboxed runtime where hardware and cluster support allow.
4. Measure resource overhead and workload compatibility for the alternative runtime.
5. Build a resource utilization and cost allocation report for the lab.

##
