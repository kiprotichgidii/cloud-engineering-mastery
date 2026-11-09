# Week 4: Container security, isolation and runtime hardening

[← Back to Month 1: Container Internals and Runtime Engineering](../README.md)

## Weekly Overview
**Goal:** Deep technical investigation and hands-on laboratory exercises for Week 4.

## Concepts to Investigate
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

## Hands-on Laboratory Modules
1. [**Lab 01 Hardened Container**](lab-01-hardened-container/README.md)
2. [**Lab 02 Seccomp**](lab-02-seccomp/README.md)
3. [**Lab 03 Rootless**](lab-03-rootless/README.md)
4. [**Lab 04 Supply Chain**](lab-04-supply-chain/README.md)

### Detailed Curriculum Specifications
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

##
