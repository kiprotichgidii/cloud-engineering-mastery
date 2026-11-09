# Week 6: Control plane, API server, etcd and controllers

[← Back to Month 2: Container Orchestration and Kubernetes Internals](../README.md)

## Weekly Overview
**Goal:** Deep technical investigation and hands-on laboratory exercises for Week 6.

## Concepts to Investigate
- API server request lifecycle.
- Authentication, authorization and admission.
- Storage through etcd.
- Watch mechanisms and resource versions.
- Controller reconciliation loops.
- Leader election.
- Informers, caches and work queues.
- API availability and control-plane failure modes.

## Hands-on Laboratory Modules
1. [**Lab 01 Api Requests**](lab-01-api-requests/README.md)
2. [**Lab 02 Etcd Investigation**](lab-02-etcd-investigation/README.md)
3. [**Lab 03 Reconciliation**](lab-03-reconciliation/README.md)
4. [**Lab 04 Controller Dev**](lab-04-controller-dev/README.md)

### Detailed Curriculum Specifications
1. **API request investigation:** Create, update and delete resources while observing API audit logs and events.
2. **etcd investigation:** Inspect cluster health, members, snapshots and restore behavior in a disposable cluster.
3. **Reconciliation experiment:** Change live resources away from desired state and observe controllers restore them.
4. **Controller development:** Build a small client-go controller that watches a resource and reconciles a simple desired state. Document retries, idempotency and error handling.

## Evidence to Commit
- API request and reconciliation sequence diagrams.
- etcd backup/restore evidence.
- Small controller source code and tests.
- Control-plane failure runbook.

## Blog Article Target
**“Kubernetes Reconciliation Explained: API Server, etcd, Watches and Controllers”**

## Exit Criteria
Explain declarative control loops and demonstrate a small controller that safely reconciles desired state.
