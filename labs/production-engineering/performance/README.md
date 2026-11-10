# Week 22: Performance engineering and capacity planning

[← Back to Month 6: Production Operations, Performance and Reliability](../README.md)

## Weekly Overview
**Goal:** Deep technical investigation and hands-on laboratory exercises for Week 22.

## Concepts to Investigate
- CPU throttling and memory pressure.
- Application profiling.
- Network and storage bottlenecks.
- Load generation and test design.
- Latency percentiles and saturation.
- HPA behavior and scaling delays.
- Resource requests and bin packing.
- Capacity headroom.

## Hands-on Laboratory Modules
1. [**Lab 01 K6 Load Testing**](lab-01-k6-load-testing/README.md)
2. [**Lab 02 Resource Limits Profiling**](lab-02-resource-limits-profiling/README.md)
3. [**Lab 03 Bottleneck Analysis**](lab-03-bottleneck-analysis/README.md)
4. [**Lab 04 Autoscaling Tuning**](lab-04-autoscaling-tuning/README.md)

### Detailed Curriculum Specifications
1. Load-test a service with k6 and establish a baseline.
2. Introduce CPU limits, memory pressure and noisy neighbors; measure impact.
3. Profile application and database bottlenecks.
4. Compare scaling strategies and tune requests, limits and HPA targets.
5. Publish a benchmark report with methodology, environment details and limitations.

## Blog Article Target
**“Kubernetes Performance Engineering: CPU Throttling, Memory Pressure and Workload Scaling”**
