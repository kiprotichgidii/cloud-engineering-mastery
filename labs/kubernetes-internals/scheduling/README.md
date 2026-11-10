# Week 7: Scheduling, resources and workload placement

[← Back to Month 2: Container Orchestration and Kubernetes Internals](../README.md)

## Weekly Overview
**Goal:** Deep technical investigation and hands-on laboratory exercises for Week 7.

## Concepts to Investigate
- Scheduler scheduling cycle.
- Filtering and scoring.
- Requests, limits and QoS.
- CPU throttling and memory OOM behavior.
- Taints, tolerations, affinity and topology spread.
- Priority and preemption.
- PodDisruptionBudgets.
- HPA, VPA and cluster autoscaling behavior.

## Hands-on Laboratory Modules
1. [**Lab 01 Scheduler Decisions**](lab-01-scheduler-decisions/README.md)
2. [**Lab 02 Resource Contention**](lab-02-resource-contention/README.md)
3. [**Lab 03 Placement Policies**](lab-03-placement-policies/README.md)
4. [**Lab 04 Autoscaling**](lab-04-autoscaling/README.md)

### Detailed Curriculum Specifications
1. **Scheduler decision lab:** Create competing workloads and inspect why Pods are filtered, scored and bound to nodes.
2. **Resource contention lab:** Generate CPU and memory pressure; inspect throttling, OOM events and node conditions.
3. **Placement policy lab:** Combine affinity, taints and topology spread; deliberately create unschedulable workloads.
4. **Autoscaling lab:** Generate load and observe HPA behavior. Measure scaling delay and distinguish application scaling from node scaling.

## Evidence to Commit
- Scheduling decision matrix.
- Resource pressure graphs and event timelines.
- Tested placement manifests.
- Autoscaling experiment report.

## Blog Article Target
**“Inside the Kubernetes Scheduler: Resource Requests, Constraints and Placement Decisions”**

## Exit Criteria
Diagnose why a Pod is unschedulable and explain the difference between resource requests, limits, application scaling and node scaling.
