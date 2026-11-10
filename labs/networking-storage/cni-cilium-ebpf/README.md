# Week 10: CNI, Cilium, eBPF and Kubernetes service routing

[← Back to Month 3: Cloud-Native Networking and Storage](../README.md)

## Weekly Overview
**Goal:** Deep technical investigation and hands-on laboratory exercises for Week 10.

## Concepts to Investigate
- CNI lifecycle and plugin responsibilities.
- Calico routing and policy model.
- Cilium architecture.
- eBPF hooks and maps.
- kube-proxy and service implementation.
- NetworkPolicy enforcement.
- Hubble observability.
- Encapsulation versus native routing.

## Hands-on Laboratory Modules
1. [**Lab 01 Cilium Install**](lab-01-cilium-install/README.md)
2. [**Lab 02 Traffic Tracing Hubble**](lab-02-traffic-tracing-hubble/README.md)
3. [**Lab 03 Network Policies**](lab-03-network-policies/README.md)
4. [**Lab 04 Calico Vs Cilium**](lab-04-calico-vs-cilium/README.md)

### Detailed Curriculum Specifications
1. Install Cilium in a disposable Kubernetes cluster and inspect its components.
2. Trace Pod-to-Pod, Pod-to-Service and external traffic using Hubble and packet captures.
3. Implement default-deny and explicitly allowed NetworkPolicies.
4. Compare selected Calico and Cilium behaviors in separate test clusters; document routing, policy and operational trade-offs.

## Blog Article Target
**“Kubernetes Networking Deep Dive: CNI, Cilium, eBPF and Service Routing”**
