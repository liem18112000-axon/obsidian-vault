---
ai_hash: 9a439061058554ee
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-11
entities:
- luz_docs_statistic
- docs-statistic-service
- Deployment
- StatefulSet
- Cloud Build
- gcloud builds triggers run
- google-skill-rollout-latest
- kubectl set image
- kubectl rollout status
- dev cluster
- luz-skill-ship
- ship.sh
- PubSub
- $facet aggregation
- EJB timer
- europe-west6-docker.pkg.dev/klara-repo/artifact-registry-container-images/luz-docs-statistic:<commit-sha>
- luz_docs_statistic updates stats via 1-minute EJB timer over PubSub and $facet aggregation
source: ship of LUZ-155460, session 2026-06-11
status: seedling
tags:
- luz
- luz-docs-statistic
- cloud-build
- kubernetes
- deploy
title: 'Shipping luz_docs_statistic: trigger is docs-statistic-service and dev runs
  a Deployment, not a StatefulSet'
type: howto
---

# Shipping luz_docs_statistic: trigger is docs-statistic-service and dev runs a Deployment, not a StatefulSet

Two facts that broke the standard luz-skill-ship flow for the luz_docs_statistic repo (discovered 2026-06-11):

1. The Cloud Build trigger is named **`docs-statistic-service`** — NOT `luz-docs-statistic` (the repo/image/workload name). Guessing the image name yields `NOT_FOUND` from `gcloud builds triggers run` for all retries.
2. In the dev cluster the workload is a **Deployment** `deployment/luz-docs-statistic` (namespace `dev`), not a StatefulSet — so `google-skill-rollout-latest` (StatefulSet-only) fails with NotFound after a successful build. Roll out manually:
   `kubectl set image deployment/luz-docs-statistic -n dev luz-docs-statistic=europe-west6-docker.pkg.dev/klara-repo/artifact-registry-container-images/luz-docs-statistic:<commit-sha>` then `kubectl rollout status`.

Working recipe: stage → `TRIGGER_NAME=docs-statistic-service ship.sh "<msg>"` (ship.sh is safe to re-run; it skips commit/push when already done) → expect the rollout step to fail → kubectl set image as above. Image tags are full commit SHAs.

Related: [[2 Areas/Kepler/Luz/luz-docs-statistic/luz_docs_statistic updates stats via 1-minute EJB timer over PubSub and $facet aggregation]]

## Related

- [[2 Areas/Kepler/Luz/luz-docs-statistic/luz_docs_statistic updates stats via 1-minute EJB timer over PubSub and $facet aggregation]]

%% ai-graph-start %%

**Related notes:**
- [[luz-docs Cloud Build pushes an image for every branch but only master updates luz_kubernetes]]
- [[luz-docs-it-staging-trigger-poller-mismatch]]
- [[luz-person is a Deployment not a StatefulSet in klara dev]]
- [[rollout-latest skill auto-detects StatefulSet vs Deployment]]
- [[luz-docs-integration-test dev poller derives the GKE job name from the wrong id]]

**Relations:**
- luz_docs_statistic — *has trigger* — docs-statistic-service
- docs-statistic-service — *is a* — Cloud Build trigger
- luz_docs_statistic — *runs as* — Deployment
- Deployment — *is in* — dev cluster
- luz_docs_statistic — *is not a* — StatefulSet
- StatefulSet — *is in* — dev cluster
- google-skill-rollout-latest — *fails on* — Deployment
- google-skill-rollout-latest — *requires* — StatefulSet
- kubectl set image — *updates* — Deployment
- luz_docs_statistic — *broke* — luz-skill-ship
- ship.sh — *is part of* — luz-skill-ship
- ship.sh — *uses variable* — TRIGGER_NAME
- luz_docs_statistic — *updates stats via* — EJB timer
- luz_docs_statistic — *updates stats via* — PubSub
- luz_docs_statistic — *updates stats via* — $facet aggregation
- luz_docs_statistic — *uses image* — europe-west6-docker.pkg.dev/klara-repo/artifact-registry-container-images/luz-docs-statistic:<commit-sha>
- luz_docs_statistic — *is related to* — luz_docs_statistic updates stats via 1-minute EJB timer over PubSub and $facet aggregation
- gcloud builds triggers run — *yields* — NOT_FOUND

%% ai-graph-end %%