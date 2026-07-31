---
title: "luz-docs Cloud Build pushes an image for every branch but only master updates luz_kubernetes"
created: 2026-06-04
type: observation
status: seedling
source: "build 915c4c8d output, session 2026-06-04"
tags: [luz-docs, cloud-build, ci-cd]
---

# luz-docs Cloud Build pushes an image for every branch but only master updates luz_kubernetes

The `luz-docs` Cloud Build trigger (project `klara-infra`, region `europe-west6`) runs on any branch: mvn package → docker build → push to `europe-west6-docker.pkg.dev/klara-repo/artifact-registry-container-images/luz-docs:<commit-sha>`. The final step (clone `luz_kubernetes`, run `update_image_version.sh`) is gated to **master only** — feature-branch builds publish an image tag but never touch the deployment manifests. So deploying a feature build to dev requires a separate rollout (e.g. google-skill-rollout-latest).
