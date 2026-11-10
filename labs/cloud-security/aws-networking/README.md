# Week 17: AWS networking and infrastructure architecture

[← Back to Month 5: Cloud-Native Security and Cloud Infrastructure](../README.md)

## Weekly Overview
**Goal:** Deep technical investigation and hands-on laboratory exercises for Week 17.

## Concepts to Investigate
- VPC design and subnet strategy.
- Route tables, NAT and VPC endpoints.
- Security Groups and Network ACLs.
- Private service access.
- Load balancers.
- IAM roles and policy evaluation.
- OpenTofu module design and state protection.

## Hands-on Laboratory Modules
1. [**Lab 01 Vpc Opentofu**](lab-01-vpc-opentofu/README.md)
2. [**Lab 02 Private Workloads**](lab-02-private-workloads/README.md)
3. [**Lab 03 Network Security Tests**](lab-03-network-security-tests/README.md)
4. [**Lab 04 Reusable Modules**](lab-04-reusable-modules/README.md)

### Detailed Curriculum Specifications
1. Provision a multi-AZ VPC using OpenTofu with public/private subnet separation.
2. Deploy a private workload and validate access through controlled paths.
3. Test routing, security groups, NACLs and VPC endpoints by deliberately breaking connectivity.
4. Build reusable modules with validation, outputs and teardown procedures.

## Blog Article Target
**“Building a Production-Oriented AWS Network with OpenTofu”**
