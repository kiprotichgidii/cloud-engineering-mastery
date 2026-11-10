# Week 23: Reliability, chaos and disaster recovery

[← Back to Month 6: Production Operations, Performance and Reliability](../README.md)

## Weekly Overview
**Goal:** Deep technical investigation and hands-on laboratory exercises for Week 23.

## Concepts to Investigate
- SLIs, SLOs and error budgets.
- Failure domains and availability.
- Graceful degradation.
- Retry, timeout and circuit-breaker behavior.
- Chaos engineering principles.
- Backup consistency.
- RTO and RPO.
- Recovery validation and incident response.

## Hands-on Laboratory Modules
1. [**Lab 01 Sli Slo Definition**](lab-01-sli-slo-definition/README.md)
2. [**Lab 02 Chaos Injection**](lab-02-chaos-injection/README.md)
3. [**Lab 03 Dns Storage Failures**](lab-03-dns-storage-failures/README.md)
4. [**Lab 04 Backup Restore Dr**](lab-04-backup-restore-dr/README.md)

### Detailed Curriculum Specifications
1. Define SLIs and SLOs for the capstone application.
2. Inject controlled Pod, node, network and dependency failures.
3. Test application behavior under DNS and storage disruptions.
4. Restore application data and platform configuration from backups.
5. Measure actual recovery time and data loss against stated RTO/RPO targets.
6. Write incident timelines and postmortems.

## Blog Article Target
**“Testing Kubernetes Resilience: Failure Injection, SLOs and Disaster Recovery”**
