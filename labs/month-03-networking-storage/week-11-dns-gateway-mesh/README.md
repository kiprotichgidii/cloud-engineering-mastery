# Week 11: DNS, ingress, Gateway API and service mesh

[← Back to Month 3: Cloud-Native Networking and Storage](../README.md)

## Weekly Overview
**Goal:** Deep technical investigation and hands-on laboratory exercises for Week 11.

## Concepts to Investigate
- CoreDNS architecture and plugin chain.
- Cluster DNS search paths.
- Service discovery.
- Ingress versus Gateway API.
- L4 and L7 load balancing.
- TLS termination and certificate automation.
- Service mesh data plane and control plane.
- mTLS and traffic policy.

## Hands-on Laboratory Modules
1. [**Lab 01 Dns Resolution**](lab-01-dns-resolution/README.md)
2. [**Lab 02 Dns Troubleshooting**](lab-02-dns-troubleshooting/README.md)
3. [**Lab 03 Gateway Api Ingress**](lab-03-gateway-api-ingress/README.md)
4. [**Lab 04 Istio Service Mesh**](lab-04-istio-service-mesh/README.md)

### Detailed Curriculum Specifications
1. Trace DNS resolution from a Pod to CoreDNS and upstream resolvers.
2. Break DNS through bad records, policy and upstream configuration; diagnose with `dig` and packet captures.
3. Deploy Gateway API or an ingress controller and test TLS, routing and backend failures.
4. Deploy a small Istio test environment; inspect sidecar or ambient traffic handling, mTLS and request telemetry.

## Blog Article Target
**“From CoreDNS to Gateway API: How Kubernetes Routes Application Traffic”**
