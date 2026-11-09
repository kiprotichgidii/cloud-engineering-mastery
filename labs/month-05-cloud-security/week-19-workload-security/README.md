# Week 19: Container and Kubernetes security engineering

[← Back to Month 5: Cloud-Native Security and Cloud Infrastructure](../README.md)

## Weekly Overview
**Goal:** Deep technical investigation and hands-on laboratory exercises for Week 19.

## Concepts to Investigate
- Container and Kubernetes threat models.
- Pod Security Admission.
- RBAC and least privilege.
- Network segmentation.
- Seccomp, AppArmor and SELinux.
- Runtime detection.
- Secrets encryption and external secrets.
- Admission policy and compliance evidence.

## Hands-on Laboratory Modules
1. [**Lab 01 Pod Security Standards**](lab-01-pod-security-standards/README.md)
2. [**Lab 02 Kyverno Policies**](lab-02-kyverno-policies/README.md)
3. [**Lab 03 Falco Detection**](lab-03-falco-detection/README.md)
4. [**Lab 04 External Secrets Vault**](lab-04-external-secrets-vault/README.md)

### Detailed Curriculum Specifications
1. Apply a hardened Pod security baseline and test prohibited configurations.
2. Deploy Kyverno or Gatekeeper policies and test both compliant and non-compliant workloads.
3. Deploy Falco and generate controlled suspicious activity to validate detections.
4. Implement External Secrets Operator or Vault integration; verify access boundaries and secret rotation behavior.
5. Perform a threat-model review of the platform and document attack paths and mitigations.

## Blog Article Target
**“Securing Kubernetes Workloads: Admission Policy, Runtime Detection and Least Privilege”**
