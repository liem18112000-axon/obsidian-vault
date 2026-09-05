---
title: "Scaling Dagster needs Postgres storage and a singleton daemon before adding replicas"
created: 2026-09-03
type: lesson
status: seedling
source: "leo-customer360 dagster-scaling-analysis, 2026-09-03"
tags: [dagster, kubernetes, scaling, gotcha]
---

# Scaling Dagster needs Postgres storage and a singleton daemon before adding replicas

A single-pod `dagster dev` (webserver + daemon + in-process code-location gRPC in one process, backed by SQLite on a ReadWriteOnce PVC) **cannot** be scaled by raising its replica count. SQLite has one writer, the volume is single-attach, and `dagster dev` is a dev-only launcher.

Production Dagster requires, **in this order**:

0. **Shared storage first.** Move run/event/schedule storage to **PostgreSQL** and compute logs to **S3/MinIO**. Until this is done, more than one replica means DB-lock errors and logs stranded on the "other" pod. This is the hard prerequisite for everything else.
1. **Split `dagster dev`** into a stateless `dagster-webserver` (N replicas, HPA-able, behind a Service) and a `dagster-daemon`.
2. **The daemon must be EXACTLY 1 replica** (`strategy: Recreate`, never HPA). Dagster has no leader election; two daemons run schedules/sensors twice → duplicate ticks and duplicate runs.

Only the webserver is stateless and horizontally scalable. Run `dagster instance migrate` after the storage switch and on every upgrade.

## Related
[[Dagster worker pools are executor queues, not a pod kind]]

## Related

- [[Dagster worker pools are executor queues]]
- [[not a pod kind]]
