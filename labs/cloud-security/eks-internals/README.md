# Week 18: EKS architecture and cloud integration

[← Back to Month 5: Cloud-Native Security and Cloud Infrastructure](../README.md)

## Weekly Overview
**Goal:** Deep technical investigation and hands-on laboratory exercises for Week 18.

## Concepts to Investigate
- EKS control-plane responsibilities.
- Managed node groups and compute choices.
- VPC CNI and Pod IP allocation.
- EKS access and authentication.
- EBS/EFS CSI.
- AWS Load Balancer Controller.
- IRSA and EKS Pod Identity.
- Cluster and node scaling.

## Hands-on Laboratory Modules
1. [**Lab 01 Eks Opentofu**](lab-01-eks-opentofu/README.md)
2. [**Lab 02 Node Bootstrap Cni**](lab-02-node-bootstrap-cni/README.md)
3. [**Lab 03 Workload Identity**](lab-03-workload-identity/README.md)
4. [**Lab 04 Csi Loadbalancer**](lab-04-csi-loadbalancer/README.md)

### Detailed Curriculum Specifications
1. Provision a disposable EKS environment using OpenTofu.
2. Inspect node bootstrap, CNI behavior and Pod IP allocation.
3. Configure workload identity and grant a workload narrowly scoped AWS access.
4. Deploy an application using EBS or EFS CSI and expose it through an AWS load balancer.
5. Test scaling, node replacement and teardown.

## Blog Article Target
**“Amazon EKS Under the Hood: Networking, Identity, Storage and Worker Nodes”**
