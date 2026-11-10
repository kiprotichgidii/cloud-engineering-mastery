# Month 1: Container Internals and Runtime Engineering

## Goal
Understand containers as operating-system processes with kernel-enforced isolation, then follow the path from an OCI image to a running process.

## Weekly Modules

- [**Week 1: Linux kernel primitives behind containers**](linux-primitives/README.md)
  - [Lab 01 Unshare Namespaces](linux-primitives/lab-01-unshare-namespaces/README.md)
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
