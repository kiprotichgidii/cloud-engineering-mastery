# Month 1 — Container Internals and Runtime Engineering

[← Back to Programme Overview](../../README.md)

> **Module objective:** Understand how Linux provides the isolation, filesystem boundaries, resource controls and security restrictions that make containers possible. Build these foundations directly with Linux tools before moving on to Docker, Podman, containerd, OCI and Kubernetes.

**Duration:** 4 weeks  
**Estimated effort:** 15–20 hours per week  
**Primary environment:** An isolated Linux virtual machine on Proxmox  
**Primary outcome:** A manually constructed container-like environment, supported by reproducible experiments, evidence, diagrams, troubleshooting notes and technical articles.

---

## 1. Month overview

A container is not a virtual machine. It is an ordinary Linux process (or group of processes) running with carefully configured isolation, resource controls and security restrictions.

Docker, Podman and Kubernetes make these mechanisms easier to consume, but the underlying capabilities are provided by the Linux kernel and related userspace components.

This month investigates those mechanisms from the ground up. The goal is not simply to learn commands. It is to understand the boundaries being created, how the kernel enforces them, how to observe their behaviour, and what happens when those boundaries are misconfigured or deliberately broken.

### Learning outcomes

By the end of this month, you should be able to:

- Explain how Linux namespaces isolate views of system resources.
- Create and inspect PID, UTS, mount, IPC, network and user namespaces.
- Construct a minimal root filesystem and understand how a process gets its own `/`.
- Explain mount propagation, `pivot_root`, bind mounts and pseudo-filesystems in a container context.
- Configure and inspect cgroups v2 resource controls for CPU, memory and process counts.
- Observe throttling, memory pressure, OOM behaviour and process-limit enforcement.
- Explain Linux capabilities and the difference between UID 0 and unrestricted host privileges.
- Compare rootful and rootless container execution and understand user-namespace mappings.
- Explain how namespaces, filesystem construction, cgroups and capabilities combine to create a container environment.
- Gather reproducible evidence using kernel interfaces, process inspection, tracing and packet capture.

### Weekly progression

| Week | Focus | Main engineering outcome | Suggested article |
|---|---|---|---|
| [1](linux-primitives/README.md) | Linux kernel primitives | Build isolated process environments with namespaces and understand the kernel interfaces behind them | Linux Namespaces Explained: Building Container Isolation from Scratch |
| [2](runtime-architecture/README.md) | Container runtime architecture and OCI | Trace container creation through Docker/containerd to OCI runtimes; run an OCI bundle | From Docker CLI to runc: Following the Lifecycle of a Container |
| [3](images-and-distribution/README.md) | Image construction and distribution | Inspect OCI images, understand OverlayFS and build/publish images with BuildKit | OCI Images Explained: Layers, Digests, OverlayFS and BuildKit in Practice |
| [4](container-security/README.md) | Container security | Harden containers using capabilities, seccomp, rootless execution and image supply-chain controls | Container Security: Rootless, Seccomp and Supply-Chain Integrity |

### Month-end deliverable

Publish a **Container Engineering Lab** repository containing:

1. Reproducible Linux isolation experiments.
2. A minimal root filesystem and container-like execution environment.
3. cgroups v2 resource-control experiments.
4. Linux capability and privilege-boundary experiments.
5. Runtime and OCI investigations.
6. Image-layer and OverlayFS investigations.
7. Container hardening and image-integrity demonstrations.
8. Architecture diagrams, evidence, cleanup scripts and troubleshooting runbooks.
9. Four technical article drafts or published articles.
10. A monthly engineering review explaining how the pieces fit together.

---

## 2. Lab environment

Use a dedicated VM for this module. Do not perform destructive mount, namespace, cgroup or runtime experiments directly on the production or primary Kubernetes nodes.

### Recommended VM specification

| Resource | Recommendation |
|---|---|
| Hypervisor | Proxmox VE |
| Operating system | Ubuntu Server 24.04 LTS or Debian 12/13 |
| CPU | 4 vCPU |
| Memory | 8 GB |
| Disk | 40–60 GB |
| Network | Proxmox bridge |
| Access | SSH and Proxmox console |
| Privileges | A user with sudo access and root access for specific exercises |

These are starting resources, not hard requirements. Increase disk or memory if later runtime, image-building or tracing exercises need them.

### Preparation and safety

1. Create a dedicated VM.
2. Apply OS updates.
3. Install the investigation tools listed below.
4. Verify the kernel and cgroup configuration.
5. Take a Proxmox snapshot named `container-internals-baseline`.
6. Keep a second SSH session or Proxmox console available when experimenting with mounts and namespaces.
7. Do not run unfamiliar cleanup commands against `/`, `/sys`, `/proc` or the host cgroup hierarchy.
8. Keep credentials, private keys, tokens and sensitive host information out of Git.

### Install investigation tools

```bash
sudo apt update

sudo apt install -y \
  util-linux \
  iproute2 \
  iptables \
  nftables \
  bridge-utils \
  cgroup-tools \
  procps \
  psmisc \
  strace \
  lsof \
  jq \
  curl \
  wget \
  git \
  build-essential \
  gcc \
  make \
  pkg-config \
  tcpdump \
  bpftool
```

Some packages or command names may differ slightly between Ubuntu and Debian releases. Record any differences in the lab notes.

### Baseline inspection

```bash
uname -a
cat /etc/os-release
findmnt /
ls -l /sys/fs/cgroup
mount | grep cgroup
```

Check whether the unified cgroup v2 hierarchy is mounted:

```bash
stat -fc %T /sys/fs/cgroup
```

Expected output:

```text
cgroup2fs
```

If the output differs, investigate the kernel and boot configuration before starting the cgroups v2 exercises. Do not assume that a cgroup v2 filesystem is available just because the directory exists.

Record the following in your evidence:

- Distribution and release.
- Kernel version.
- Architecture.
- VM CPU and memory allocation.
- Cgroup filesystem type and mount options.
- Installed versions of relevant tools.
- Whether the VM is running nested inside another virtualization layer.

---

## Weekly Modules

- [**Week 1: Linux kernel primitives behind containers**](linux-primitives/README.md)
  - [Lab 01 Linux Namespaces](linux-primitives/lab-01-linux-namespaces/README.md)
  - [Lab 02 Minimal Rootfs](linux-primitives/lab-02-minimal-rootfs/README.md)
  - [Lab 03 Cgroups V2](linux-primitives/lab-03-cgroups-v2/README.md)
  - [Lab 04 Capabilities](linux-primitives/lab-04-capabilities/README.md)
- [**Week 2: Container runtime architecture and OCI**](runtime-architecture/README.md)
  - [Lab 01 Docker Tracing](runtime-architecture/lab-01-docker-tracing/README.md)
  - [Lab 02 Containerd](runtime-architecture/lab-02-containerd/README.md)
  - [Lab 03 Oci Bundle](runtime-architecture/lab-03-oci-bundle/README.md)
  - [Lab 04 Runc Vs Crun](runtime-architecture/lab-04-runc-vs-crun/README.md)
- [**Week 3: Image construction, storage and distribution**](images-and-distribution/README.md)
  - [Lab 01 Image Dissection](images-and-distribution/lab-01-image-dissection/README.md)
  - [Lab 02 Overlayfs](images-and-distribution/lab-02-overlayfs/README.md)
  - [Lab 03 Buildkit](images-and-distribution/lab-03-buildkit/README.md)
  - [Lab 04 Private Registry](images-and-distribution/lab-04-private-registry/README.md)
- [**Week 4: Container security, isolation and runtime hardening**](container-security/README.md)
  - [Lab 01 Hardened Container](container-security/lab-01-hardened-container/README.md)
  - [Lab 02 Seccomp](container-security/lab-02-seccomp/README.md)
  - [Lab 03 Rootless](container-security/lab-03-rootless/README.md)
  - [Lab 04 Supply Chain](container-security/lab-04-supply-chain/README.md)
