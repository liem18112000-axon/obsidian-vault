---
title: "Cloud Build $COMMIT_SHA is the full 40-char git SHA, not the short one"
created: 2026-07-03
type: lesson
status: seedling
source: "Vinnstack GKE deploy 2026-07-03"
tags: [gcp, cloud-build, ci-cd, gotcha]
---

# Cloud Build $COMMIT_SHA is the full 40-char git SHA, not the short one

The `$COMMIT_SHA` substitution Cloud Build injects into a build is the **full 40-character** git commit SHA, not the abbreviated 7-char form. So images tagged `...:$COMMIT_SHA` in the pipeline land in the registry under the full SHA.

**Consequence:** a hand-rolled deploy script that resolves the image tag with `git rev-parse --short HEAD` builds a tag that does not exist in Artifact Registry, and the rollout fails with `ImagePullBackOff` / `ErrImagePull`. Use `git rev-parse HEAD` (full) to match Cloud Build's convention.

Verify what actually exists with `gcloud artifacts docker tags list <repo-path>` before deploying.

## Related

- [[GKE Immediate-binding StorageClass deadlocks a single-replica StatefulSet across zones]]
