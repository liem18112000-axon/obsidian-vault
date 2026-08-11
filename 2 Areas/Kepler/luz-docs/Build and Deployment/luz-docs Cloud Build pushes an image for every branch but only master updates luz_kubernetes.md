---
ai_hash: e4463fbb2f57ed06
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-04
entities:
- luz-docs
- Cloud Build
- klara-infra
- europe-west6
- mvn package
- docker build
- europe-west6-docker.pkg.dev/klara-repo/artifact-registry-container-images/luz-docs
- commit-sha
- luz_kubernetes
- update_image_version.sh
- master
- feature-branch
- deployment manifests
- google-skill-rollout-latest
source: build 915c4c8d output, session 2026-06-04
status: seedling
tags:
- luz-docs
- cloud-build
- ci-cd
title: luz-docs Cloud Build pushes an image for every branch but only master updates
  luz_kubernetes
type: observation
---

# luz-docs Cloud Build pushes an image for every branch but only master updates luz_kubernetes

The `luz-docs` Cloud Build trigger (project `klara-infra`, region `europe-west6`) runs on any branch: mvn package → docker build → push to `europe-west6-docker.pkg.dev/klara-repo/artifact-registry-container-images/luz-docs:<commit-sha>`. The final step (clone `luz_kubernetes`, run `update_image_version.sh`) is gated to **master only** — feature-branch builds publish an image tag but never touch the deployment manifests. So deploying a feature build to dev requires a separate rollout (e.g. google-skill-rollout-latest).

%% ai-graph-start %%

**Related notes:**
- [[Shipping luz_docs_statistic trigger is docs-statistic-service and dev runs a Deployment, not a StatefulSet]]
- [[Klara Cloud Build pushes images to klara-repo Artifact Registry with the SA on the trigger]]
- [[luz-docs-it-staging-trigger-poller-mismatch]]
- [[luz-docs-integration-test dev poller derives the GKE job name from the wrong id]]
- [[rollout-latest skill auto-detects StatefulSet vs Deployment]]

**Relations:**
- luz-docs — *uses* — Cloud Build
- Cloud Build — *is in project* — klara-infra
- Cloud Build — *is in region* — europe-west6
- Cloud Build — *runs* — mvn package
- Cloud Build — *runs* — docker build
- Cloud Build — *pushes to* — europe-west6-docker.pkg.dev/klara-repo/artifact-registry-container-images/luz-docs
- europe-west6-docker.pkg.dev/klara-repo/artifact-registry-container-images/luz-docs — *is tagged with* — commit-sha
- Cloud Build — *clones* — luz_kubernetes
- Cloud Build — *runs* — update_image_version.sh
- update_image_version.sh — *is gated to* — master
- master — *updates* — luz_kubernetes
- feature-branch — *publishes* — image tag
- feature-branch — *does not touch* — deployment manifests
- master — *updates* — deployment manifests
- google-skill-rollout-latest — *is a type of* — separate rollout
- separate rollout — *is required for* — feature-branch

%% ai-graph-end %%