# Week 14: Helm, Kustomize and configuration engineering

[← Back to Month 4: Container Platforms and Application Delivery](../README.md)

## Weekly Overview
**Goal:** Deep technical investigation and hands-on laboratory exercises for Week 14.

## Concepts to Investigate
- Helm rendering and release lifecycle.
- Chart design and dependencies.
- Values schema and validation.
- Kustomize bases and overlays.
- Secret handling.
- Configuration drift.
- Template testing and policy validation.

## Hands-on Laboratory Modules
1. [**Lab 01 Helm Packaging**](lab-01-helm-packaging/README.md)
2. [**Lab 02 Kustomize Overlays**](lab-02-kustomize-overlays/README.md)
3. [**Lab 03 Manifest Validation**](lab-03-manifest-validation/README.md)
4. [**Lab 04 Policy Violation Tests**](lab-04-policy-violation-tests/README.md)

### Detailed Curriculum Specifications
1. Package a real application as a Helm chart with configurable resources, probes, services and ingress/Gateway API.
2. Build dev, staging and production overlays with Kustomize.
3. Add schema validation, linting and manifest tests.
4. Deliberately introduce invalid configuration and policy violations; ensure the pipeline catches them before deployment.

## Blog Article Target
**“Engineering Reusable Kubernetes Deployments with Helm and Kustomize”**
