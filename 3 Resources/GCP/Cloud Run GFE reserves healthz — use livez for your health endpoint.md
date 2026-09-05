---
title: "Cloud Run GFE reserves /healthz — use /livez for your health endpoint"
created: 2026-08-27
type: lesson
status: seedling
source: "session 2026-08-27 — kga deploy"
tags: [cloud-run, gfe, health-check, gcp, gotcha]
---

# Cloud Run GFE reserves /healthz — use /livez for your health endpoint

On Cloud Run, the Google Front End (GFE) **reserves the path `/healthz`** and returns its OWN HTML 404 (`Error 404 (Not Found)!!1`, Content-Type text/html) BEFORE the request reaches your container. So an app route at `/healthz` works locally (uvicorn/TestClient) but is unreachable externally on Cloud Run — while a sibling route like `/readyz` at the same router works fine.

**How to tell:** the 404 body is a Google-branded HTML page (not your apps JSON/plain 404). A truly-undefined path in your app returns YOUR 404 (or your middlewares 401), proving it reached the container; `/healthz` returning the Google page proves it did NOT.

**Fix:** name your liveness endpoint something else — `/livez` (or `/_healthz` etc.) — and point the Cloud Run startup/liveness probes at that path too.

**Second gotcha (probe mismatch):** the startup probe path is part of the service spec. If you rename the app endpoint but deploy a new image WITHOUT updating the probe path (e.g. via `gcloud run services update --image` while the spec still probes `/healthz`), the new revision fails the startup probe → "container failed to start" → traffic stays on the old revision. Update the probe (terraform apply) in the same change as the rename.

Context: kga Cloud Run service (LUZ-159671 test-agent).

## Related

- [[Cloud Run v2 has startup_probe + liveness_probe]]
- [[no readiness probe]]
