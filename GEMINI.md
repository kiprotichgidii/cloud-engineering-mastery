# Project Context: Advanced Cloud-Native Engineering Laboratory Programme

## 1. Project Purpose
The **Advanced Cloud-Native Engineering Laboratory Programme** is an intensive, 24-week practical engineering and research curriculum spanning from container low-level internals (namespaces, cgroups, runc, OCI, CRI) to production-grade distributed systems (Kubernetes orchestration, Cilium/eBPF networking, Rook-Ceph storage, GitOps, cloud security on AWS/EKS, and chaos/reliability engineering).

The learning paradigm is: **Build, inspect, instrument, break, recover, explain and publish**.

## 2. Architecture Overview
- **Primary Environment:** Proxmox/KVM homelab cluster.
  - Dedicated Runtime VM (Docker, Podman, containerd, runc, crun, BuildKit).
  - Dedicated Kernel VM (namespaces, cgroups, syscall tracing, eBPF).
  - Kubernetes cluster (1 control plane, 2 worker nodes) provisioned via `kubeadm`.
  - Optional local OCI registry VM.
- **Secondary Cloud Environment:** AWS / EKS provisioned with OpenTofu / Terraform for Months 5 & 6.
- **Key Systems:** Cilium (eBPF CNI), Rook-Ceph (CSI), Argo CD (GitOps), Kyverno/Falco (Security), OpenTelemetry/Prometheus/Grafana/Loki (Observability).

## 3. Repository Map
```text
cloud-native-engineering-labs/
├── docs/             # Programme roadmaps, architecture decisions, research notes, runbooks
├── labs/             # 24-week hands-on laboratory modules (4 labs/week)
├── platform/         # Infrastructure as code, Ansible playbooks, K8s manifests, GitOps
├── applications/     # Sample microservices (API, worker, frontend) for lab testing
├── scripts/          # Cluster bootstrap, validation, and cleanup automation
├── assessments/      # Monthly self-evaluations and proof-of-competence evidence
├── blog/             # Weekly technical articles, draft diagrams, published posts
├── reviews/          # Weekly and monthly retrospective reviews
└── capstone/         # Final production platform implementation & disaster recovery
```

## 4. Local Setup & Workflow
- Use dedicated VMs or isolated test nodes for kernel-level or destructive failure experiments.
- Always test setup, failure injection, and cleanup scripts (`setup.sh`, `validate.sh`, `cleanup.sh`) before marking a lab complete.
- All lab deliverables must include documented evidence (logs, traces, CLI captures) in the lab's `evidence/` directory.

## 5. Lab Engineering Conventions
Each lab follows the standard directory format:
- `README.md`: Objective, research questions, prerequisites, architecture, steps, failure mode, and findings.
- `architecture/diagram.mmd`: Mermaid diagram illustrating components and boundaries.
- `manifests/`: Declarative YAML/JSON/HCL definitions.
- `scripts/`: `setup.sh`, `validate.sh`, `cleanup.sh`.
- `evidence/`: `command-output.md`, `observations.md`.
- `troubleshooting.md`: Root cause analysis and step-by-step resolution.
