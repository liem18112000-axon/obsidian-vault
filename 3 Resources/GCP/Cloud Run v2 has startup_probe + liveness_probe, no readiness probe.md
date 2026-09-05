---
title: "Cloud Run v2 has startup_probe + liveness_probe, no readiness probe"
created: 2026-08-27
type: lesson
status: seedling
source: "session 2026-08-27 — kga deployments/main.tf"
tags: [cloud-run, terraform, health-check, gcp, gotcha]
---

# Cloud Run v2 has startup_probe + liveness_probe, no readiness probe

Cloud Run v2 (`google_cloud_run_v2_service`) supports **`startup_probe`** and **`liveness_probe`** inside `template.containers`, but there is **no readiness probe** (unlike Kubernetes) — Cloud Run gates traffic on the startup probe instead. So an app can expose `/readyz` for humans/monitoring, but Terraform only wires `/healthz`-style liveness + startup.

Details that matter:
- `http_get.port` **defaults to the container serving port** (8080 / the injected `$PORT`), so you can omit it.
- **`startup_probe.failure_threshold * period_seconds`** is your cold-start budget — e.g. `failure_threshold=10`, `period_seconds=5` ≈ 50s before the revision is marked failed. Set this generously for slow imports.
- Liveness restarts the container on repeated failure; keep its `period_seconds` longer (e.g. 30s) to avoid churn.
- Both take `http_get { path = "/healthz" }`; the health handler must return 200 fast and cheap (no dependency calls) — put dependency checks behind a separate readiness endpoint.
