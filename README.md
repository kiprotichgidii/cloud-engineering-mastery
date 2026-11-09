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

# 3. Weekly laboratory curriculum

# Month 1 — Container Internals and Runtime Engineering

**Goal:** Understand containers as operating-system processes with kernel-enforced isolation, then follow the path from an OCI image to a running process.

## Week 1 — Linux kernel primitives behind containers

### Concepts to investigate
- Process model, PID namespaces and process trees.
- Mount, UTS, IPC, network and user namespaces.
- Namespace membership and `/proc/<pid>/ns`.
- cgroups v1 versus unified cgroups v2.
- Resource controllers: CPU, memory, pids and I/O.
- Linux capabilities and privilege boundaries.
- `chroot` versus `pivot_root`.
- Mount propagation and filesystem isolation.

### Labs

**Lab 1.1 — Build a container-like environment using `unshare`**
- Create isolated UTS, PID, mount, IPC and network namespaces.
- Use `unshare` to start a shell in the new namespaces.
- Mount a separate `/proc` and inspect the process tree.
- Compare namespace inode identifiers between host and isolated processes.
- Enter and inspect namespaces with `nsenter`.
- Document which resources are isolated and which remain shared.

**Lab 1.2 — Construct a minimal root filesystem**
- Create a minimal root filesystem using a small Linux distribution rootfs.
- Use `mount --bind`, `pivot_root` and mount namespaces to change the process root.
- Mount `/proc`, `/sys` and `/dev` appropriately.
- Run a process inside the new root.
- Demonstrate what a root filesystem does and does not isolate.
- Compare the result with a normal container.

**Lab 1.3 — Enforce resource limits with cgroups v2**
- Create a cgroup and move a test process into it.
- Configure CPU, memory and PID limits.
- Generate CPU and memory pressure.
- Inspect `cpu.stat`, `memory.current`, `memory.events` and `pids.current`.
- Observe throttling and OOM behavior.
- Compare host-level and cgroup-level measurements.

**Lab 1.4 — Linux capabilities and privilege boundaries**
- Inspect capabilities using `capsh` and `/proc`.
- Run processes with reduced capabilities.
- Test operations that require capabilities such as `NET_ADMIN` and `SYS_ADMIN`.
- Demonstrate why privileged containers are dangerous.
- Build a least-privilege capability profile for a sample service.

### Evidence to commit
- Namespace and cgroup diagrams.
- Shell scripts for creating and cleaning the experiments.
- Output showing namespace identifiers and cgroup counters.
- A table mapping container features to Linux kernel primitives.

### Blog article
**“Linux Containers Under the Hood: Namespaces, cgroups and the Kernel Primitives That Make Isolation Possible”**

### Exit criteria
Explain how namespaces isolate views of system resources, how cgroups account for and limit resources, and why neither mechanism alone provides a complete security boundary.

---

## Week 2 — Container runtime architecture and OCI

### Concepts to investigate
- OCI image, runtime and distribution specifications.
- Docker Engine architecture.
- containerd daemon, namespaces, content store, snapshots and tasks.
- CRI and the Kubernetes runtime integration boundary.
- runc and crun.
- Runtime lifecycle: create, start, exec, pause, resume, kill and delete.
- Shim processes and why they exist.
- Runtime state and bundle configuration.

### Labs

**Lab 2.1 — Trace Docker from CLI to process**
- Run Docker in debug mode in an isolated VM.
- Trace a container creation request from the CLI to the daemon.
- Inspect daemon logs and container metadata.
- Identify when image resolution, snapshot preparation and runtime creation occur.
- Correlate the resulting host process with container metadata.

**Lab 2.2 — Operate containerd directly**
- Install containerd and nerdctl.
- Pull an image into containerd.
- Inspect namespaces, images, content and snapshots with `ctr`.
- Run a container using `ctr` and compare with nerdctl.
- Inspect task and shim processes.
- Explain the difference between containerd namespaces and Linux namespaces.

**Lab 2.3 — Inspect and execute an OCI bundle**
- Create an OCI bundle using a runtime tooling workflow.
- Inspect `config.json`, root filesystem and process configuration.
- Run the bundle using runc.
- Change namespace, mount, capability and resource settings.
- Observe the effect of each change.
- Cleanly delete the runtime state.

**Lab 2.4 — Compare runc and crun**
- Run the same OCI workload with runc and crun.
- Compare lifecycle behavior, configuration and diagnostics.
- Measure startup time with a repeatable method.
- Record version and environment details.
- Avoid claiming one runtime is universally faster from a small test.

### Evidence to commit
- Docker-to-runtime sequence diagram.
- OCI bundle with explanatory annotations.
- containerd inspection output.
- Runtime comparison report and reproducible benchmark script.

### Blog article
**“From Docker CLI to runc: Following the Lifecycle of a Container”**

### Exit criteria
Explain the responsibilities of Docker Engine, containerd, CRI, OCI runtimes and shim processes, and demonstrate running a workload without relying on Docker.

---

## Week 3 — Image construction, storage and distribution

### Concepts to investigate
- OCI image manifest, config and layers.
- Content-addressable storage and digests.
- Layer reuse and copy-on-write.
- OverlayFS lower, upper, merged and work directories.
- BuildKit architecture and build graph.
- Build cache, cache mounts and remote cache.
- Multi-platform images and manifests.
- Registries, authentication and content distribution.
- Image signing and SBOMs.

### Labs

**Lab 3.1 — Dissect an OCI image**
- Pull a selected image using Skopeo or a compatible tool.
- Inspect its manifest, config and layer digests.
- Export and inspect its filesystem layers.
- Identify environment variables, entrypoint, command, user and working directory.
- Compare image digest with mutable tags.
- Explain why a tag is not an immutable identity.

**Lab 3.2 — Inspect OverlayFS and container writable layers**
- Run a container and locate its snapshot or storage directories.
- Inspect OverlayFS mount options.
- Modify files inside the container and identify where changes appear.
- Compare a writable container with a read-only image layer.
- Remove the container and observe the lifecycle of writable data.
- Explain why persistent application data should not depend on the writable layer.

**Lab 3.3 — Engineer a reproducible BuildKit pipeline**
- Build an application using multi-stage builds.
- Use BuildKit cache mounts and secret mounts correctly.
- Compare build time and image size before and after optimization.
- Build for multiple architectures where the environment permits.
- Export and reuse a remote build cache.
- Check that secrets are not embedded in image layers or build output.

**Lab 3.4 — Operate an OCI registry**
- Deploy a private registry.
- Push, pull and inspect images.
- Configure authentication and TLS.
- Test digest-based deployment.
- Explore retention and garbage collection.
- Simulate registry unavailability and observe the effect on new and already-running workloads.

### Evidence to commit
- Image layer inspection report.
- OverlayFS diagrams and command output.
- Before/after image size and build-time results.
- Registry deployment configuration and verification steps.

### Blog article
**“OCI Images Explained: Layers, Digests, OverlayFS and BuildKit in Practice”**

### Exit criteria
Explain image metadata, content addressing, layer reuse, writable snapshots, registry operations and reproducible image builds.

---

## Week 4 — Container security, isolation and runtime hardening

### Concepts to investigate
- Rootless containers and user namespaces.
- Capability bounding and ambient sets.
- Seccomp profiles.
- AppArmor and SELinux concepts.
- Read-only root filesystems.
- Mount and device restrictions.
- Container escape threat models.
- Image vulnerability management.
- SBOMs, signing and provenance.
- Runtime detection and audit evidence.

### Labs

**Lab 4.1 — Build a hardened container profile**
- Run an application as a non-root user.
- Drop all unnecessary capabilities.
- Enable `no-new-privileges`.
- Apply a read-only root filesystem.
- Use a temporary writable mount only where needed.
- Set CPU, memory and PID limits.
- Verify each control from inside and outside the container.

**Lab 4.2 — Develop and test a seccomp profile**
- Trace the syscalls used by a sample application.
- Create a restrictive seccomp profile.
- Run the application with the profile.
- Introduce a required syscall that is denied.
- Inspect the failure and kernel audit/log evidence.
- Refine the profile without blindly allowing every syscall.

**Lab 4.3 — Compare rootful and rootless execution**
- Run equivalent workloads using rootful and rootless modes.
- Inspect user namespace mappings.
- Compare networking, storage and device access limitations.
- Test the operational trade-offs.
- Document workloads that may need additional configuration.

**Lab 4.4 — Build a container supply-chain workflow**
- Scan a container image.
- Generate an SBOM.
- Sign an image using Cosign in a test environment.
- Verify the signature before deployment.
- Demonstrate rejection of an unsigned or altered image.
- Document key management and trust assumptions.

### Month 1 deliverable — Container Engineering Lab

Produce a cohesive repository that demonstrates:
- Linux isolation primitives.
- OCI runtime lifecycle.
- Direct containerd and OCI runtime usage.
- Image and layer inspection.
- BuildKit image optimization.
- Private registry operation.
- Rootless and hardened containers.
- Image scanning, SBOM and signature verification.

**Month 1 technical publication:** A four-part container engineering series, one article per week, plus a long-form architecture summary.

---

# Month 2 — Container Orchestration and Kubernetes Internals

**Goal:** Understand how Kubernetes consumes container runtimes and coordinates workloads across nodes.

## Week 5 — CRI, kubelet and container lifecycle

### Concepts to investigate
- Kubernetes CRI architecture.
- Kubelet responsibilities.
- CRI-O versus containerd integration.
- Pod sandbox and pause containers.
- Image service and runtime service.
- Pod lifecycle and termination.
- Probes, restart behavior and graceful shutdown.
- Static Pods and node-level components.

### Labs
1. **CRI inspection:** Use `crictl` to inspect runtime information, images, Pods and containers directly on a Kubernetes node.
2. **Pod sandbox investigation:** Trace Pod creation from API request through kubelet, CRI, containerd and OCI runtime.
3. **Runtime comparison:** Configure a disposable cluster with containerd and investigate CRI-O as a comparison environment.
4. **Lifecycle failure lab:** Reproduce image-pull, sandbox creation, startup, probe and termination failures; diagnose each from node and control-plane evidence.

### Evidence
- Pod creation sequence diagram.
- CRI inspection scripts.
- Runtime logs and failure analysis.
- Container lifecycle troubleshooting runbook.

### Blog article
**“How Kubernetes Runs Containers: From Pod Creation to the OCI Runtime”**

### Exit criteria
Trace a Pod from the Kubernetes API to the process running on a node and identify which component owns each stage.

## Week 6 — Control plane, API server, etcd and controllers

### Concepts to investigate
- API server request lifecycle.
- Authentication, authorization and admission.
- Storage through etcd.
- Watch mechanisms and resource versions.
- Controller reconciliation loops.
- Leader election.
- Informers, caches and work queues.
- API availability and control-plane failure modes.

### Labs
1. **API request investigation:** Create, update and delete resources while observing API audit logs and events.
2. **etcd investigation:** Inspect cluster health, members, snapshots and restore behavior in a disposable cluster.
3. **Reconciliation experiment:** Change live resources away from desired state and observe controllers restore them.
4. **Controller development:** Build a small client-go controller that watches a resource and reconciles a simple desired state. Document retries, idempotency and error handling.

### Evidence
- API request and reconciliation sequence diagrams.
- etcd backup/restore evidence.
- Small controller source code and tests.
- Control-plane failure runbook.

### Blog article
**“Kubernetes Reconciliation Explained: API Server, etcd, Watches and Controllers”**

### Exit criteria
Explain declarative control loops and demonstrate a small controller that safely reconciles desired state.

## Week 7 — Scheduling, resources and workload placement

### Concepts to investigate
- Scheduler scheduling cycle.
- Filtering and scoring.
- Requests, limits and QoS.
- CPU throttling and memory OOM behavior.
- Taints, tolerations, affinity and topology spread.
- Priority and preemption.
- PodDisruptionBudgets.
- HPA, VPA and cluster autoscaling behavior.

### Labs
1. **Scheduler decision lab:** Create competing workloads and inspect why Pods are filtered, scored and bound to nodes.
2. **Resource contention lab:** Generate CPU and memory pressure; inspect throttling, OOM events and node conditions.
3. **Placement policy lab:** Combine affinity, taints and topology spread; deliberately create unschedulable workloads.
4. **Autoscaling lab:** Generate load and observe HPA behavior. Measure scaling delay and distinguish application scaling from node scaling.

### Evidence
- Scheduling decision matrix.
- Resource pressure graphs and event timelines.
- Tested placement manifests.
- Autoscaling experiment report.

### Blog article
**“Inside the Kubernetes Scheduler: Resource Requests, Constraints and Placement Decisions”**

### Exit criteria
Diagnose why a Pod is unschedulable and explain the difference between resource requests, limits, application scaling and node scaling.

## Week 8 — Kubernetes operations, upgrades and recovery

### Concepts to investigate
- kubeadm lifecycle.
- Certificate management and rotation.
- Control-plane and node upgrades.
- etcd backup and restore.
- Node drain and disruption.
- Static Pod recovery.
- Version skew and API deprecation.
- Cluster troubleshooting methodology.

### Labs
1. **Cluster upgrade rehearsal:** Upgrade a disposable kubeadm cluster one supported version step at a time.
2. **Node failure exercise:** Drain, stop and recover a worker; observe workload rescheduling and disruption.
3. **Control-plane recovery:** Simulate a recoverable control-plane or etcd failure and follow a documented recovery procedure.
4. **Certificate and API maintenance:** Inspect certificate expiry and deprecated API usage; rehearse safe maintenance.

### Month 2 deliverable — Kubernetes Runtime and Operations Lab

A repeatable kubeadm cluster with:
- Containerd/CRI inspection.
- Pod lifecycle tracing.
- Control-plane and etcd investigation.
- A small custom controller.
- Scheduling and resource experiments.
- Upgrade and recovery runbooks.

**Month 2 publication:** Four deep technical articles and a Kubernetes internals architecture guide.

---

# Month 3 — Cloud-Native Networking and Storage

**Goal:** Understand packet flow, service connectivity and persistent data across container and Kubernetes boundaries.

## Week 9 — Linux networking for containers

### Concepts to investigate
- Network namespaces, veth pairs and bridges.
- Routing, ARP and neighbor discovery.
- NAT, conntrack and firewall rules.
- iptables and nftables.
- VXLAN and encapsulation.
- MTU and fragmentation.
- DNS resolution and packet capture.

### Labs
1. Build two isolated network namespaces connected by a veth pair and Linux bridge.
2. Configure routing and NAT between namespaces.
3. Capture and inspect ARP, DNS, TCP handshakes and HTTP traffic.
4. Break routes, firewall rules and MTU settings; diagnose with `ip`, `ss`, `tcpdump`, `tracepath` and conntrack tools.

### Blog article
**“Linux Networking for Containers: Namespaces, veth, Bridges, NAT and Packet Flow”**

## Week 10 — CNI, Cilium, eBPF and Kubernetes service routing

### Concepts to investigate
- CNI lifecycle and plugin responsibilities.
- Calico routing and policy model.
- Cilium architecture.
- eBPF hooks and maps.
- kube-proxy and service implementation.
- NetworkPolicy enforcement.
- Hubble observability.
- Encapsulation versus native routing.

### Labs
1. Install Cilium in a disposable Kubernetes cluster and inspect its components.
2. Trace Pod-to-Pod, Pod-to-Service and external traffic using Hubble and packet captures.
3. Implement default-deny and explicitly allowed NetworkPolicies.
4. Compare selected Calico and Cilium behaviors in separate test clusters; document routing, policy and operational trade-offs.

### Blog article
**“Kubernetes Networking Deep Dive: CNI, Cilium, eBPF and Service Routing”**

## Week 11 — DNS, ingress, Gateway API and service mesh

### Concepts to investigate
- CoreDNS architecture and plugin chain.
- Cluster DNS search paths.
- Service discovery.
- Ingress versus Gateway API.
- L4 and L7 load balancing.
- TLS termination and certificate automation.
- Service mesh data plane and control plane.
- mTLS and traffic policy.

### Labs
1. Trace DNS resolution from a Pod to CoreDNS and upstream resolvers.
2. Break DNS through bad records, policy and upstream configuration; diagnose with `dig` and packet captures.
3. Deploy Gateway API or an ingress controller and test TLS, routing and backend failures.
4. Deploy a small Istio test environment; inspect sidecar or ambient traffic handling, mTLS and request telemetry.

### Blog article
**“From CoreDNS to Gateway API: How Kubernetes Routes Application Traffic”**

## Week 12 — CSI, distributed storage and stateful workloads

### Concepts to investigate
- CSI controller and node services.
- Volume provisioning, attach, mount and expansion.
- StorageClasses and reclaim policies.
- Rook-Ceph architecture.
- Replication, quorum and failure domains.
- StatefulSets and application consistency.
- Snapshots, backups and restore.
- Storage performance and latency.

### Labs
1. Trace a PVC from claim creation through provisioning and mount.
2. Deploy or extend a Rook-Ceph test cluster and inspect Ceph health, MON, MGR and OSD behavior.
3. Simulate a storage component or node failure and observe degraded operation and recovery.
4. Benchmark persistent storage using a controlled workload; compare latency and throughput under different conditions.
5. Test snapshot and restore for a stateful application; verify application-level data consistency.

### Month 3 deliverable — Cloud-Native Data Plane Lab

A Kubernetes environment with:
- Documented Linux packet flow.
- Cilium and eBPF observability.
- NetworkPolicy tests.
- DNS and Gateway API routing.
- CSI provisioning.
- Rook-Ceph health and recovery experiments.
- Stateful application backup and restore evidence.

**Month 3 publication:** Four articles covering Linux networking, CNI/eBPF, Kubernetes traffic routing and CSI/distributed storage.

---

# Month 4 — Container Platforms and Application Delivery

**Goal:** Engineer the complete path from source code to a verified workload running on Kubernetes.

## Week 13 — Advanced image build and supply-chain pipeline

### Concepts to investigate
- BuildKit architecture and cache model.
- Reproducible builds.
- Multi-platform builds.
- Private registry authentication.
- SBOM generation.
- Image signing and provenance.
- Artifact promotion and digest pinning.

### Labs
1. Build a multi-service application with optimized multi-stage images and reproducible build inputs.
2. Create a CI pipeline with tests, BuildKit caching, SBOM generation and vulnerability scanning.
3. Sign image digests and verify signatures before promotion.
4. Promote the same immutable artifact between development, staging and production-like environments.

### Blog article
**“Building a Secure Container Supply Chain with BuildKit, SBOMs and Cosign”**

## Week 14 — Helm, Kustomize and configuration engineering

### Concepts to investigate
- Helm rendering and release lifecycle.
- Chart design and dependencies.
- Values schema and validation.
- Kustomize bases and overlays.
- Secret handling.
- Configuration drift.
- Template testing and policy validation.

### Labs
1. Package a real application as a Helm chart with configurable resources, probes, services and ingress/Gateway API.
2. Build dev, staging and production overlays with Kustomize.
3. Add schema validation, linting and manifest tests.
4. Deliberately introduce invalid configuration and policy violations; ensure the pipeline catches them before deployment.

### Blog article
**“Engineering Reusable Kubernetes Deployments with Helm and Kustomize”**

## Week 15 — GitOps and progressive delivery

### Concepts to investigate
- GitOps principles and reconciliation.
- Argo CD architecture.
- ApplicationSets and app-of-apps trade-offs.
- Sync waves and health checks.
- Drift detection and self-healing.
- Rollback and promotion.
- Canary and blue/green delivery concepts.

### Labs
1. Bootstrap Argo CD and manage cluster applications declaratively.
2. Build an ApplicationSet for multiple environments or clusters.
3. Introduce manual drift and observe reconciliation.
4. Implement a controlled promotion and rollback workflow; optionally add a progressive delivery controller.

### Blog article
**“Operating Kubernetes with GitOps: Argo CD Reconciliation, Drift and Promotion”**

## Week 16 — Platform engineering and developer self-service

### Concepts to investigate
- Internal Developer Platform (IDP).
- Golden paths and templates.
- Self-service provisioning.
- Platform APIs and developer experience.
- Backstage concepts.
- Policy as Code.
- Multi-environment and multi-team boundaries.

### Labs
1. Build a reusable application onboarding template that creates repository structure, CI workflow, Helm chart and GitOps application.
2. Add policy checks for resource limits, approved registries, non-root execution and required labels.
3. Create a simple developer self-service workflow using templates or a lightweight portal.
4. Test onboarding with a second sample application and document where the platform still requires manual intervention.

### Month 4 deliverable — Developer Delivery Platform

A working platform that takes application source through testing, image build, scanning, signing, registry publication and GitOps deployment, with reusable templates and policy checks.

**Month 4 publication:** Four articles on supply-chain automation, Helm/Kustomize, GitOps and platform engineering.

---

# Month 5 — Cloud-Native Security and Cloud Infrastructure

**Goal:** Apply container and Kubernetes engineering knowledge to cloud infrastructure, workload identity and multi-tenant security.

## Week 17 — AWS networking and infrastructure architecture

### Concepts to investigate
- VPC design and subnet strategy.
- Route tables, NAT and VPC endpoints.
- Security Groups and Network ACLs.
- Private service access.
- Load balancers.
- IAM roles and policy evaluation.
- OpenTofu module design and state protection.

### Labs
1. Provision a multi-AZ VPC using OpenTofu with public/private subnet separation.
2. Deploy a private workload and validate access through controlled paths.
3. Test routing, security groups, NACLs and VPC endpoints by deliberately breaking connectivity.
4. Build reusable modules with validation, outputs and teardown procedures.

### Blog article
**“Building a Production-Oriented AWS Network with OpenTofu”**

## Week 18 — EKS architecture and cloud integration

### Concepts to investigate
- EKS control-plane responsibilities.
- Managed node groups and compute choices.
- VPC CNI and Pod IP allocation.
- EKS access and authentication.
- EBS/EFS CSI.
- AWS Load Balancer Controller.
- IRSA and EKS Pod Identity.
- Cluster and node scaling.

### Labs
1. Provision a disposable EKS environment using OpenTofu.
2. Inspect node bootstrap, CNI behavior and Pod IP allocation.
3. Configure workload identity and grant a workload narrowly scoped AWS access.
4. Deploy an application using EBS or EFS CSI and expose it through an AWS load balancer.
5. Test scaling, node replacement and teardown.

### Blog article
**“Amazon EKS Under the Hood: Networking, Identity, Storage and Worker Nodes”**

## Week 19 — Container and Kubernetes security engineering

### Concepts to investigate
- Container and Kubernetes threat models.
- Pod Security Admission.
- RBAC and least privilege.
- Network segmentation.
- Seccomp, AppArmor and SELinux.
- Runtime detection.
- Secrets encryption and external secrets.
- Admission policy and compliance evidence.

### Labs
1. Apply a hardened Pod security baseline and test prohibited configurations.
2. Deploy Kyverno or Gatekeeper policies and test both compliant and non-compliant workloads.
3. Deploy Falco and generate controlled suspicious activity to validate detections.
4. Implement External Secrets Operator or Vault integration; verify access boundaries and secret rotation behavior.
5. Perform a threat-model review of the platform and document attack paths and mitigations.

### Blog article
**“Securing Kubernetes Workloads: Admission Policy, Runtime Detection and Least Privilege”**

## Week 20 — Multi-tenancy, isolation and cloud cost engineering

### Concepts to investigate
- Namespace-based tenancy and its limitations.
- RBAC boundaries.
- Network isolation.
- Resource quotas and limit ranges.
- Dedicated nodes and taints.
- Runtime isolation options such as gVisor and Kata Containers.
- Node and workload density.
- Requests versus actual utilization.
- Cost allocation and chargeback concepts.

### Labs
1. Design two tenant environments with separate identities, quotas, policies and network boundaries.
2. Attempt controlled cross-tenant access and verify that the controls prevent it.
3. Compare standard containers with an alternative sandboxed runtime where hardware and cluster support allow.
4. Measure resource overhead and workload compatibility for the alternative runtime.
5. Build a resource utilization and cost allocation report for the lab.

### Month 5 deliverable — Secure Cloud-Native Environment

A reproducible cloud environment with:
- Network isolation and infrastructure as code.
- EKS workload integration.
- Workload identity.
- Container and admission security.
- Runtime detection.
- Secrets management.
- Tenant isolation tests.
- Cost and resource analysis.

**Month 5 publication:** Four articles on AWS networking, EKS internals, Kubernetes security and multi-tenancy/runtime isolation.

---

# Month 6 — Production Operations, Performance and Reliability

**Goal:** Demonstrate that the platform can be observed, measured, stress-tested, diagnosed and recovered.

## Week 21 — Metrics, logs, traces and OpenTelemetry

### Concepts to investigate
- Prometheus scraping and service discovery.
- PromQL and recording rules.
- Grafana dashboards.
- Alertmanager routing and inhibition.
- Kubernetes and application metrics.
- Loki log collection and labels.
- OpenTelemetry SDK, Collector and pipelines.
- Trace context propagation.
- Tempo or Jaeger.

### Labs
1. Instrument a sample application with metrics and distributed traces.
2. Deploy Prometheus, Grafana, Loki and an OpenTelemetry Collector.
3. Correlate a request across application logs, metrics and traces.
4. Create alerts for latency, errors, saturation and workload health; test alert quality and noise.

### Blog article
**“Building Full-Stack Observability for Kubernetes with Prometheus and OpenTelemetry”**

## Week 22 — Performance engineering and capacity planning

### Concepts to investigate
- CPU throttling and memory pressure.
- Application profiling.
- Network and storage bottlenecks.
- Load generation and test design.
- Latency percentiles and saturation.
- HPA behavior and scaling delays.
- Resource requests and bin packing.
- Capacity headroom.

### Labs
1. Load-test a service with k6 and establish a baseline.
2. Introduce CPU limits, memory pressure and noisy neighbors; measure impact.
3. Profile application and database bottlenecks.
4. Compare scaling strategies and tune requests, limits and HPA targets.
5. Publish a benchmark report with methodology, environment details and limitations.

### Blog article
**“Kubernetes Performance Engineering: CPU Throttling, Memory Pressure and Workload Scaling”**

## Week 23 — Reliability, chaos and disaster recovery

### Concepts to investigate
- SLIs, SLOs and error budgets.
- Failure domains and availability.
- Graceful degradation.
- Retry, timeout and circuit-breaker behavior.
- Chaos engineering principles.
- Backup consistency.
- RTO and RPO.
- Recovery validation and incident response.

### Labs
1. Define SLIs and SLOs for the capstone application.
2. Inject controlled Pod, node, network and dependency failures.
3. Test application behavior under DNS and storage disruptions.
4. Restore application data and platform configuration from backups.
5. Measure actual recovery time and data loss against stated RTO/RPO targets.
6. Write incident timelines and postmortems.

### Blog article
**“Testing Kubernetes Resilience: Failure Injection, SLOs and Disaster Recovery”**

## Week 24 — Capstone integration and engineering review

### Labs
1. Deploy the complete platform from documented infrastructure and GitOps repositories.
2. Onboard a new sample application using the developer workflow.
3. Run security, policy, performance and observability checks.
4. Execute a controlled failure game day and demonstrate recovery.
5. Conduct an architecture review covering trade-offs, limitations, cost and operational risks.
6. Publish the capstone documentation and a final technical retrospective.

### Month 6 deliverable — Production-Ready Demonstration Platform

The final platform must demonstrate:
- Repeatable infrastructure provisioning.
- Secure container build and distribution.
- Kubernetes workload deployment.
- Network and storage integration.
- GitOps application delivery.
- Identity and policy enforcement.
- Metrics, logs and distributed traces.
- Load testing and capacity evidence.
- Failure injection and recovery.
- Runbooks, SLOs and architecture documentation.

**Month 6 publication:** Three deep technical articles plus a complete capstone case study.

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
│   ├── month-01-container-internals/
│   │   ├── week-01-linux-primitives/
│   │   │   ├── lab-01-unshare-namespaces/
│   │   │   ├── lab-02-minimal-rootfs/
│   │   │   ├── lab-03-cgroups-v2/
│   │   │   └── lab-04-capabilities/
│   │   ├── week-02-runtime-architecture/
│   │   │   ├── lab-01-docker-tracing/
│   │   │   ├── lab-02-containerd/
│   │   │   ├── lab-03-oci-bundle/
│   │   │   └── lab-04-runc-vs-crun/
│   │   ├── week-03-images-and-distribution/
│   │   │   ├── lab-01-image-dissection/
│   │   │   ├── lab-02-overlayfs/
│   │   │   ├── lab-03-buildkit/
│   │   │   └── lab-04-private-registry/
│   │   └── week-04-container-security/
│   │       ├── lab-01-hardened-container/
│   │       ├── lab-02-seccomp/
│   │       ├── lab-03-rootless/
│   │       └── lab-04-supply-chain/
│   ├── month-02-kubernetes-internals/
│   │   ├── week-05-cri-kubelet/
│   │   ├── week-06-control-plane/
│   │   ├── week-07-scheduling/
│   │   └── week-08-operations-recovery/
│   ├── month-03-networking-storage/
│   │   ├── week-09-linux-networking/
│   │   ├── week-10-cni-cilium-ebpf/
│   │   ├── week-11-dns-gateway-mesh/
│   │   └── week-12-csi-rook-ceph/
│   ├── month-04-platform-engineering/
│   │   ├── week-13-image-supply-chain/
│   │   ├── week-14-helm-kustomize/
│   │   ├── week-15-gitops-delivery/
│   │   └── week-16-developer-platform/
│   ├── month-05-cloud-security/
│   │   ├── week-17-aws-networking/
│   │   ├── week-18-eks-internals/
│   │   ├── week-19-workload-security/
│   │   └── week-20-tenancy-isolation/
│   └── month-06-production-engineering/
│       ├── week-21-observability/
│       ├── week-22-performance/
│       ├── week-23-reliability-recovery/
│       └── week-24-capstone/
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
