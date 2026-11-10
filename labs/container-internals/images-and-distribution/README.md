# Week 3: Image construction, storage and distribution

[← Back to Month 1: Container Internals and Runtime Engineering](../README.md)

## Weekly Overview
**Goal:** Deep technical investigation and hands-on laboratory exercises for Week 3.

## Concepts to Investigate
- OCI image manifest, config and layers.
- Content-addressable storage and digests.
- Layer reuse and copy-on-write.
- OverlayFS lower, upper, merged and work directories.
- BuildKit architecture and build graph.
- Build cache, cache mounts and remote cache.
- Multi-platform images and manifests.
- Registries, authentication and content distribution.
- Image signing and SBOMs.

## Hands-on Laboratory Modules
1. [**Lab 01 Image Dissection**](lab-01-image-dissection/README.md)
2. [**Lab 02 Overlayfs**](lab-02-overlayfs/README.md)
3. [**Lab 03 Buildkit**](lab-03-buildkit/README.md)
4. [**Lab 04 Private Registry**](lab-04-private-registry/README.md)

### Detailed Curriculum Specifications
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

## Evidence to Commit
- Image layer inspection report.
- OverlayFS diagrams and command output.
- Before/after image size and build-time results.
- Registry deployment configuration and verification steps.

## Blog Article Target
**“OCI Images Explained: Layers, Digests, OverlayFS and BuildKit in Practice”**

## Exit Criteria
Explain image metadata, content addressing, layer reuse, writable snapshots, registry operations and reproducible image builds.
