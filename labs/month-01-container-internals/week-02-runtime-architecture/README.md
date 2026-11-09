# Week 2: Container runtime architecture and OCI

[← Back to Month 1: Container Internals and Runtime Engineering](../README.md)

## Weekly Overview
**Goal:** Deep technical investigation and hands-on laboratory exercises for Week 2.

## Concepts to Investigate
- OCI image, runtime and distribution specifications.
- Docker Engine architecture.
- containerd daemon, namespaces, content store, snapshots and tasks.
- CRI and the Kubernetes runtime integration boundary.
- runc and crun.
- Runtime lifecycle: create, start, exec, pause, resume, kill and delete.
- Shim processes and why they exist.
- Runtime state and bundle configuration.

## Hands-on Laboratory Modules
1. [**Lab 01 Docker Tracing**](lab-01-docker-tracing/README.md)
2. [**Lab 02 Containerd**](lab-02-containerd/README.md)
3. [**Lab 03 Oci Bundle**](lab-03-oci-bundle/README.md)
4. [**Lab 04 Runc Vs Crun**](lab-04-runc-vs-crun/README.md)

### Detailed Curriculum Specifications
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

## Evidence to Commit
- Docker-to-runtime sequence diagram.
- OCI bundle with explanatory annotations.
- containerd inspection output.
- Runtime comparison report and reproducible benchmark script.

## Blog Article Target
**“From Docker CLI to runc: Following the Lifecycle of a Container”**

## Exit Criteria
Explain the responsibilities of Docker Engine, containerd, CRI, OCI runtimes and shim processes, and demonstrate running a workload without relying on Docker.
