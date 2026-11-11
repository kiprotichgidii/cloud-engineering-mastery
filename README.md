# Advanced Cloud-Native Engineering Laboratory Programme

> **Duration:** 24 weeks | **Target period:** 9 November 2026 – 8 May 2027  
> **Level:** Intermediate to advanced; assumes practical Linux, Docker, Kubernetes and IaC experience  
> **Learning model:** Build, inspect, instrument, break, recover, explain and publish  
> **Primary emphasis:** Container internals and the technologies that make containers useful in cloud-native platforms

## Programme philosophy

This is not a beginner DevOps course. It is a practical engineering and research programme designed to build deep understanding of how containerized workloads are created, isolated, executed, distributed, secured, orchestrated and operated.

Every weekly module should produce:

1. A reproducible hands-on lab.
2. Evidence: commands, configuration, test results, traces, packet captures or source-code changes.
3. At least one deliberate failure and a documented diagnosis.
4. Architecture diagrams and engineering notes.
5. A technical article suitable for publication.
6. A short review explaining what was learned and what remains unclear.

Aim for approximately **70% lab work, 20% technical investigation and 10% documentation/publishing**. Do not mark a lab complete because a tutorial worked once. Rebuild it from a clean state and explain why it works.

---

# 1. Programme map

| Month | Domain | Monthly engineering deliverable |
|---|---|---|
| 1 | Container internals and runtime engineering | A container runtime investigation lab, secure image build pipeline and container isolation demonstrations |
| 2 | Container orchestration and Kubernetes internals | A kubeadm cluster with runtime integration, control-plane investigations, scheduling experiments and recovery runbooks |
| 3 | Cloud-native networking and storage | A network and storage platform with CNI experiments, eBPF observability, CSI and failure/recovery evidence |
| 4 | Container platforms and application delivery | A GitOps-based application platform with a complete image-to-production delivery pipeline |
| 5 | Cloud-native security and cloud infrastructure | A secured AWS/EKS or equivalent cloud platform with workload identity, supply-chain controls and isolation tests |
| 6 | Production operations and reliability engineering | A fully observable, load-tested, failure-tested cloud-native platform with SLOs and disaster-recovery evidence |

## Weekly completion standard

A weekly module is complete only when:

- [ ] The lab can be provisioned from documented prerequisites.
- [ ] The implementation works from a clean or reset environment.
- [ ] At least one failure scenario has been reproduced.
- [ ] Diagnosis is supported by evidence, not guesswork.
- [ ] Cleanup and rollback procedures are tested.
- [ ] Diagrams and technical notes are committed.
- [ ] A blog draft is complete and technically reviewed.
- [ ] The learner can explain the design and trade-offs without reading the tutorial.

---

# 2. Laboratory environment

Use the existing Proxmox/KVM homelab as the primary environment. Keep destructive kernel and runtime experiments isolated from the main Kubernetes cluster.

## Suggested environment

| Environment | Suggested starting allocation | Purpose |
|---|---:|---|
| Runtime VM | 4 vCPU / 8 GB RAM | Docker, Podman, containerd, BuildKit, runc, crun |
| Kernel VM | 4 vCPU / 8 GB RAM | namespaces, cgroups, syscall tracing, eBPF |
| Kubernetes control plane | 4 vCPU / 8 GB RAM | kubeadm control plane |
| Kubernetes worker 1 | 4 vCPU / 8 GB RAM | workloads and networking |
| Kubernetes worker 2 | 4 vCPU / 8 GB RAM | workloads, scheduling and failure tests |
| Optional registry VM | 2 vCPU / 4 GB RAM | OCI registry, distribution and caching |

Adjust these allocations to available resources. Use snapshots before risky experiments. Do not run kernel crash, resource exhaustion or network-disruption exercises against a production or shared homelab cluster.

## Core toolset

- Linux: `iproute2`, `nftables`, `iptables`, `nsenter`, `unshare`, `mount`, `findmnt`, `strace`, `perf`, `bpftool`, `bpftrace`
- Containers: Docker Engine, Podman, Buildah, Skopeo, containerd, nerdctl, runc, crun, BuildKit
- Images and supply chain: ORAS, Syft, Grype or Trivy, Cosign
- Kubernetes: kubeadm, kubectl, crictl, kubelet, Helm, Kustomize
- Networking: Cilium, Calico, Wireshark/tcpdump, CoreDNS, Gateway API
- Storage: CSI, Rook-Ceph, Linux device mapper and filesystem tools
- Delivery: GitHub Actions or Jenkins, Argo CD, OCI registry
- Security: seccomp, AppArmor, SELinux where available, Kyverno, Falco
- Observability: Prometheus, Grafana, Loki, OpenTelemetry, Tempo or Jaeger
- Cloud: OpenTofu/Terraform, AWS CLI, EKS, ECR, IAM

Pin tool versions in lab documentation. Record the tested OS, kernel and runtime versions because behavior can differ between releases.

---

---

# 3. Weekly laboratory curriculum overview

The complete 24-week engineering curriculum is organized into modular directories under [`labs/`](labs/). Each lab contains full reproducible manifests, setup/validation scripts, evidence records, and failure runbooks.

### Monthly Roadmap Summary

| Month | Domain Focus | Engineering Deliverable | Directory |
|---|---|---|---|
| **Month 1** | Container internals & runtime engineering | Container runtime lab, image build pipeline, isolation demos | [`labs/container-internals/`](labs/container-internals/README.md) |
| **Month 2** | Container orchestration & Kubernetes internals | Kubeadm cluster, runtime integration, scheduling experiments | [`labs/kubernetes-internals/`](labs/kubernetes-internals/README.md) |
| **Month 3** | Cloud-native networking & storage | CNI experiments, eBPF observability, CSI & Ceph recovery | [`labs/networking-storage/`](labs/networking-storage/README.md) |
| **Month 4** | Container platforms & application delivery | GitOps application platform & image-to-production pipeline | [`labs/platform-engineering/`](labs/platform-engineering/README.md) |
| **Month 5** | Cloud-native security & cloud infrastructure | Secured AWS/EKS platform, workload identity & supply chain | [`labs/cloud-security/`](labs/cloud-security/README.md) |
| **Month 6** | Production operations & reliability engineering | Observable, load-tested platform with SLOs & DR evidence | [`labs/production-engineering/`](labs/production-engineering/README.md) |

### 24-Week Laboratory Directory Map

| Week | Focus Area | Labs | Lab Directory |
|:---:|---|---|:---:|
| **01** | Linux kernel primitives | Linux namespaces, minimal rootfs, cgroups v2, capabilities | [Week 01](labs/container-internals/linux-primitives/README.md) |
| **02** | Runtime architecture & OCI | Docker tracing, containerd, OCI bundles, runc vs crun | [Week 02](labs/container-internals/runtime-architecture/README.md) |
| **03** | Image storage & distribution | OCI image dissection, OverlayFS, BuildKit, private registry | [Week 03](labs/container-internals/images-and-distribution/README.md) |
| **04** | Security & runtime hardening | Hardened profiles, seccomp, rootless containers, supply chain | [Week 04](labs/container-internals/container-security/README.md) |
| **05** | CRI, kubelet & lifecycle | CRI inspection, Pod sandbox, runtime comparison, lifecycle failures | [Week 05](labs/kubernetes-internals/cri-kubelet/README.md) |
| **06** | Control plane & controllers | API request audit, etcd snapshots, reconciliation, controller dev | [Week 06](labs/kubernetes-internals/control-plane/README.md) |
| **07** | Scheduling & placement | Scheduler decisions, resource contention, placement policy, HPA | [Week 07](labs/kubernetes-internals/scheduling/README.md) |
| **08** | Operations & recovery | Cluster upgrades, node failure, control plane recovery, certs | [Week 08](labs/kubernetes-internals/operations-recovery/README.md) |
| **09** | Linux container networking | veth pairs & bridges, routing/NAT, packet capture, troubleshooting | [Week 09](labs/networking-storage/linux-networking/README.md) |
| **10** | CNI, Cilium & eBPF | Cilium install, Hubble tracing, network policies, Calico comparison | [Week 10](labs/networking-storage/cni-cilium-ebpf/README.md) |
| **11** | DNS, Ingress & Gateway API | CoreDNS resolution, DNS failures, Gateway API, Istio mTLS | [Week 11](labs/networking-storage/dns-gateway-mesh/README.md) |
| **12** | CSI & distributed storage | PVC lifecycle, Rook-Ceph, storage failure recovery, benchmarking | [Week 12](labs/networking-storage/csi-rook-ceph/README.md) |
| **13** | Image build & supply chain | Multi-service build, CI pipeline & SBOM, Cosign signing, promotion | [Week 13](labs/platform-engineering/image-supply-chain/README.md) |
| **14** | Helm & Kustomize | Production Helm charts, Kustomize overlays, schema linting, CI tests | [Week 14](labs/platform-engineering/helm-kustomize/README.md) |
| **15** | GitOps delivery | Argo CD bootstrap, ApplicationSets, drift self-healing, Rollouts | [Week 15](labs/platform-engineering/gitops-delivery/README.md) |
| **16** | Platform self-service | Onboarding template, policy checks, self-service portals, multi-app | [Week 16](labs/platform-engineering/developer-platform/README.md) |
| **17** | AWS networking IaC | Multi-AZ VPC OpenTofu, private subnets, security groups, modules | [Week 17](labs/cloud-security/aws-networking/README.md) |
| **18** | EKS architecture | EKS OpenTofu, VPC CNI & node bootstrap, IRSA/Pod identity, EBS CSI | [Week 18](labs/cloud-security/eks-internals/README.md) |
| **19** | Kubernetes security | Pod Security Standards, Kyverno policies, Falco runtime, Vault | [Week 19](labs/cloud-security/workload-security/README.md) |
| **20** | Multi-tenancy & isolation | Multi-tenant quotas, network boundaries, gVisor/Kata, benchmarks | [Week 20](labs/cloud-security/tenancy-isolation/README.md) |
| **21** | Observability stack | OpenTelemetry instrumentation, Prometheus/Loki/Grafana, tracing | [Week 21](labs/production-engineering/observability/README.md) |
| **22** | Performance engineering | k6 load testing, CPU/memory throttling, perf/eBPF bottlenecks | [Week 22](labs/production-engineering/performance/README.md) |
| **23** | Chaos & disaster recovery | SLIs/SLOs, Chaos Mesh injection, CoreDNS outages, Velero recovery | [Week 23](labs/production-engineering/reliability-recovery/README.md) |
| **24** | Capstone integration | Full platform deploy, app onboarding, compliance audit, game day | [Week 24](labs/production-engineering/capstone/README.md) |

> 💡 **Detailed Lab Guides:** For complete step-by-step instructions, research questions, implementation code, architecture diagrams, scripts, and troubleshooting guides, navigate directly into each weekly directory or lab module above.

---

# 4. Monthly deliverables and review gates

## Month 1 — Container Engineering Lab
**Required evidence**
- [ ] Namespace and cgroup experiments.
- [ ] Minimal root filesystem and `pivot_root` exercise.
- [ ] Direct containerd and OCI runtime execution.
- [ ] OCI image and OverlayFS investigation.
- [ ] BuildKit pipeline and private registry.
- [ ] Hardened/rootless container examples.
- [ ] SBOM and image signature verification.
- [ ] Four weekly articles and one monthly summary.

**Review gate:** Can I explain and demonstrate how a Linux process becomes a container, how its filesystem is assembled, how resources are controlled and how its privileges are constrained?

## Month 2 — Kubernetes Runtime and Operations Lab
**Required evidence**
- [ ] kubelet-to-CRI-to-runtime lifecycle trace.
- [ ] etcd backup and restore.
- [ ] Small working controller.
- [ ] Scheduling and resource pressure experiments.
- [ ] Cluster upgrade and recovery rehearsal.
- [ ] Four weekly articles and one monthly summary.

**Review gate:** Can I trace a Pod from the API server to the process on a node and diagnose failures at each layer?

## Month 3 — Cloud-Native Data Plane Lab
**Required evidence**
- [ ] Linux network namespace and routing experiments.
- [ ] Cilium and eBPF investigation.
- [ ] NetworkPolicy and DNS tests.
- [ ] Gateway API/TLS configuration.
- [ ] CSI provisioning and Rook-Ceph recovery.
- [ ] Stateful backup and restore.
- [ ] Four weekly articles and one monthly summary.

**Review gate:** Can I trace a request through the network and explain how persistent data is provisioned, mounted, replicated and recovered?

## Month 4 — Developer Delivery Platform
**Required evidence**
- [ ] Automated test and image build pipeline.
- [ ] SBOM, scan and signature verification.
- [ ] Helm chart and Kustomize overlays.
- [ ] Argo CD reconciliation and rollback.
- [ ] Reusable application onboarding template.
- [ ] Policy checks.
- [ ] Four weekly articles and one monthly summary.

**Review gate:** Can another developer deploy a new application through the platform without manually assembling every deployment component?

## Month 5 — Secure Cloud-Native Environment
**Required evidence**
- [ ] OpenTofu-managed cloud network.
- [ ] EKS cluster and workload integration.
- [ ] Workload identity and least privilege.
- [ ] Admission and runtime security.
- [ ] Secrets management.
- [ ] Tenant isolation tests.
- [ ] Cost and utilization report.
- [ ] Four weekly articles and one monthly summary.

**Review gate:** Can I explain and demonstrate how cloud identity, network boundaries, workload policies and runtime controls protect the platform?

## Month 6 — Production Engineering Platform
**Required evidence**
- [ ] Metrics, logs and traces.
- [ ] Actionable alerting.
- [ ] Load and performance report.
- [ ] SLO definitions and error budget.
- [ ] Failure game day.
- [ ] Tested backup and recovery.
- [ ] Incident reports and runbooks.
- [ ] Capstone case study and final review.

**Review gate:** Can I operate the platform, detect meaningful problems, explain their impact and recover within documented objectives?

---

# 5. Technical blogging and publishing workflow

Treat each lab as the research behind a technical article. Do not write an article that merely repeats installation commands.

## Suggested article structure

```markdown
# Article title

## Problem and motivation
What engineering problem or question is being investigated?

## Background
Explain the relevant architecture and concepts.

## Lab environment
OS, kernel, runtime, tool versions and infrastructure.

## Architecture
Include a diagram of the components and interactions.

## Implementation
Explain the steps, configurations and important decisions.

## Investigation
Show commands, logs, traces, packet captures or source-code findings.

## Failure scenarios
Describe what was broken, the observed symptoms and the root cause.

## Results
Include measurements and evidence. Explain the limitations.

## Security and operational considerations
Discuss risks, trade-offs and production implications.

## Lessons learned
Summarize what the experiment demonstrated.

## Reproduction and cleanup
Provide steps for readers to repeat and safely remove the lab.

## References
Link to official documentation, specifications and source code.
```

## Publishing standards

- Prefer original experiments over generic tool introductions.
- Include architecture and sequence diagrams.
- Record exact versions and environment details.
- Include actual command output or test evidence where useful.
- Explain why a configuration is needed.
- Distinguish observed results from assumptions.
- Include cleanup and security warnings.
- Never publish credentials, private keys, tokens, internal addresses or sensitive logs.
- Link to upstream specifications, documentation and source code.
- Re-run the lab from a clean state before publishing.

## Suggested monthly article series

| Month | Series theme |
|---|---|
| 1 | Container Engineering: From Linux Kernel to OCI Runtime |
| 2 | Kubernetes Internals: From CRI to Reconciliation |
| 3 | Cloud-Native Data Plane: Networking, eBPF and Storage |
| 4 | Engineering a GitOps Developer Platform |
| 5 | Securing Cloud-Native Workloads on AWS/EKS |
| 6 | Operating Reliable Cloud-Native Platforms |

---

# 6. Recommended GitHub repository structure

```text
cloud-native-engineering-labs/
├── README.md
├── LICENSE
├── .gitignore
├── docs/
│   ├── programme/
│   │   ├── six-month-roadmap.md
│   │   ├── lab-standards.md
│   │   └── monthly-review-template.md
│   ├── architecture/
│   │   ├── diagrams/
│   │   ├── decisions/
│   │   └── platform-overview.md
│   ├── research-notes/
│   │   ├── linux/
│   │   ├── containers/
│   │   ├── kubernetes/
│   │   ├── networking/
│   │   ├── storage/
│   │   ├── security/
│   │   └── cloud/
│   └── runbooks/
├── labs/
│   ├── container-internals/
│   │   ├── linux-primitives/
│   │   │   ├── lab-01-linux-namespaces/
│   │   │   ├── lab-02-minimal-rootfs/
│   │   │   ├── lab-03-cgroups-v2/
│   │   │   └── lab-04-capabilities/
│   │   ├── runtime-architecture/
│   │   │   ├── lab-01-docker-tracing/
│   │   │   ├── lab-02-containerd/
│   │   │   ├── lab-03-oci-bundle/
│   │   │   └── lab-04-runc-vs-crun/
│   │   ├── images-and-distribution/
│   │   │   ├── lab-01-image-dissection/
│   │   │   ├── lab-02-overlayfs/
│   │   │   ├── lab-03-buildkit/
│   │   │   └── lab-04-private-registry/
│   │   └── container-security/
│   │       ├── lab-01-hardened-container/
│   │       ├── lab-02-seccomp/
│   │       ├── lab-03-rootless/
│   │       └── lab-04-supply-chain/
│   ├── kubernetes-internals/
│   │   ├── cri-kubelet/
│   │   ├── control-plane/
│   │   ├── scheduling/
│   │   └── operations-recovery/
│   ├── networking-storage/
│   │   ├── linux-networking/
│   │   ├── cni-cilium-ebpf/
│   │   ├── dns-gateway-mesh/
│   │   └── csi-rook-ceph/
│   ├── platform-engineering/
│   │   ├── image-supply-chain/
│   │   ├── helm-kustomize/
│   │   ├── gitops-delivery/
│   │   └── developer-platform/
│   ├── cloud-security/
│   │   ├── aws-networking/
│   │   ├── eks-internals/
│   │   ├── workload-security/
│   │   └── tenancy-isolation/
│   └── production-engineering/
│       ├── observability/
│       ├── performance/
│       ├── reliability-recovery/
│       └── capstone/
├── platform/
│   ├── infrastructure/
│   │   ├── modules/
│   │   └── environments/
│   ├── ansible/
│   ├── kubernetes/
│   ├── gitops/
│   ├── security/
│   └── observability/
├── applications/
│   ├── sample-api/
│   ├── sample-worker/
│   └── sample-frontend/
├── scripts/
│   ├── bootstrap/
│   ├── validation/
│   └── cleanup/
├── assessments/
│   ├── month-01/
│   ├── month-02/
│   ├── month-03/
│   ├── month-04/
│   ├── month-05/
│   └── month-06/
├── blog/
│   ├── drafts/
│   ├── diagrams/
│   └── published/
├── reviews/
│   ├── weekly/
│   └── monthly/
└── capstone/
    ├── architecture/
    ├── infrastructure/
    ├── applications/
    ├── gitops/
    ├── security/
    ├── observability/
    ├── tests/
    └── disaster-recovery/
```

Create directories as you use them; Git does not track empty directories.

## Lab directory standard

Each lab should be independently reproducible and use a consistent layout:

```text
lab-01-example/
├── README.md
├── architecture/
│   └── diagram.mmd
├── manifests/
├── scripts/
│   ├── setup.sh
│   ├── validate.sh
│   └── cleanup.sh
├── evidence/
│   ├── command-output.md
│   └── observations.md
└── troubleshooting.md
```

Not every lab needs every directory. Keep it simple where appropriate.

## Lab README template

```markdown
# Lab: [Title]

## Objective
What engineering question will this lab answer?

## Research questions
- What do I expect to discover?
- Which component or boundary am I investigating?

## Prerequisites
- OS and kernel version
- Required tools and versions
- Infrastructure and resource requirements
- Required knowledge

## Architecture
Describe the components and include a diagram.

## Implementation
Provide reproducible steps and explain important configuration choices.

## Investigation
Include commands, logs, traces, packet captures or source-code findings.

## Failure injection
What can be deliberately broken? What symptoms are expected?

## Troubleshooting and root cause
Explain the diagnosis with evidence.

## Validation
Define measurable checks that prove the lab works.

## Cleanup and rollback
Explain how to restore the environment safely.

## Findings and trade-offs
What did the experiment prove? What are its limitations?

## Blog article
Link to the draft or published article.

## References
Official documentation, specifications and source code.
```

---

# 7. Weekly execution rhythm

| Session | Suggested time | Work |
|---|---:|---|
| Technical reading and source investigation | 2 hours | Specifications, docs, source code and architecture |
| Lab implementation | 3 hours | Build the environment and implement the experiment |
| Deep investigation | 3 hours | Trace, inspect, benchmark and break components |
| Rebuild and validation | 2 hours | Reset and reproduce the lab |
| Documentation and diagrams | 2 hours | Record findings and architecture |
| Blog writing | 2 hours | Produce a technical draft |
| Review and assessment | 1 hour | Explain the result and identify gaps |
| **Total** | **15 hours** | |

If a lab needs more time, carry it forward. Do not rush a difficult investigation just to keep the calendar.

---

# 8. Final engineering standard

At the end of six months, the repository should demonstrate more than successful deployments. It should show that the engineer can investigate systems at multiple layers:

- **Kernel:** namespaces, cgroups, capabilities, filesystems and syscalls.
- **Runtime:** OCI, containerd, runc/crun, CRI and lifecycle.
- **Image:** layers, digests, registries, builds and supply-chain integrity.
- **Orchestration:** Kubernetes API, kubelet, controllers, scheduler and etcd.
- **Data plane:** CNI, eBPF, DNS, routing, Gateway API and CSI.
- **Platform:** CI/CD, GitOps, policy, developer self-service and automation.
- **Cloud:** VPC, IAM, EKS, workload identity and cloud integration.
- **Operations:** observability, performance, security, reliability and recovery.

The final test is simple: given an unfamiliar failure in a containerized cloud-native system, can I identify the layer where it occurs, gather the right evidence, explain the root cause, implement a safe fix and prevent recurrence?

> **The objective is not to collect tools or finish tutorials. It is to understand the systems deeply enough to engineer, operate and explain them.**
