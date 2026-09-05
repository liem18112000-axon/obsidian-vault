---
title: "Dagster worker pools are executor queues, not a pod kind"
created: 2026-09-03
type: concept
status: seedling
source: "leo-customer360 dagster-scaling-analysis, 2026-09-03"
tags: [dagster, kubernetes, celery, executor, scaling]
---

# Dagster worker pools are executor queues, not a pod kind

In Dagster, a "worker pool" is **not a kind of pod** — it is execution capacity expressed as a **run launcher + executor + concurrency queue**. A conceptual spec like `workers: {ai: {replicas: 10}}` maps to one of two real shapes:

- **Fixed Celery pools** (`celery_k8s_job_executor` + a broker like Redis): one long-lived Celery **worker Deployment per queue**, subscribed with `dagster-celery worker start -q <pool>`. Here `replicas` is literal and pods stay warm (no per-run cold start) — but you pay for idle capacity.
- **Ephemeral K8s Jobs** (`K8sRunLauncher` + `k8s_job_executor`): each run/step becomes a K8s Job, created on demand and torn down; node pools autoscale (scale-to-zero). No standing replicas — the "replica count" becomes a node-pool/concurrency ceiling. This is the modern default and fits batch/scheduled work.

Either way you need a **`QueuedRunCoordinator`** with `tag_concurrency_limits` to enforce per-pool caps, and you **route jobs to pools** by tagging each job with `dagster/pool` plus `dagster-k8s/config` (resources) and a `nodeSelector`.

**Gotcha:** classify pools by the *binding resource*, not just vCPU. Memory-bound jobs — large in-memory joins, Polars full-scans — belong in a **high-memory** pool even at modest CPU, or they OOM.

## Related
[[Scaling Dagster needs Postgres storage and a singleton daemon before adding replicas]]

## Related

- [[Scaling Dagster needs Postgres storage and a singleton daemon before adding replicas]]
