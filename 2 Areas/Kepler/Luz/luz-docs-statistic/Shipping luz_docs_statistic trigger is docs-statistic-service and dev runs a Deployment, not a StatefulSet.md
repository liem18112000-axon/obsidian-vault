---
title: "Shipping luz_docs_statistic: trigger is docs-statistic-service and dev runs a Deployment, not a StatefulSet"
created: 2026-06-11
type: howto
status: seedling
source: "ship of LUZ-155460, session 2026-06-11"
tags: [luz, luz-docs-statistic, cloud-build, kubernetes, deploy]
---

# Shipping luz_docs_statistic: trigger is docs-statistic-service and dev runs a Deployment, not a StatefulSet

Two facts that broke the standard luz-skill-ship flow for the luz_docs_statistic repo (discovered 2026-06-11):

1. The Cloud Build trigger is named **`docs-statistic-service`** — NOT `luz-docs-statistic` (the repo/image/workload name). Guessing the image name yields `NOT_FOUND` from `gcloud builds triggers run` for all retries.
2. In the dev cluster the workload is a **Deployment** `deployment/luz-docs-statistic` (namespace `dev`), not a StatefulSet — so `google-skill-rollout-latest` (StatefulSet-only) fails with NotFound after a successful build. Roll out manually:
   `kubectl set image deployment/luz-docs-statistic -n dev luz-docs-statistic=europe-west6-docker.pkg.dev/klara-repo/artifact-registry-container-images/luz-docs-statistic:<commit-sha>` then `kubectl rollout status`.

Working recipe: stage → `TRIGGER_NAME=docs-statistic-service ship.sh "<msg>"` (ship.sh is safe to re-run; it skips commit/push when already done) → expect the rollout step to fail → kubectl set image as above. Image tags are full commit SHAs.

Related: [[luz_docs_statistic updates stats via 1-minute EJB timer over Pub/Sub and $facet aggregation]]

## Related

- [[luz_docs_statistic updates stats via 1-minute EJB timer over Pub/Sub and $facet aggregation]]
