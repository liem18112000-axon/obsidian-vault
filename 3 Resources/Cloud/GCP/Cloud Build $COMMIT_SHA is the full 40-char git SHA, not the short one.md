---
ai_hash: f93cb33f46afd949
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-03
entities: []
source: Vinnstack GKE deploy 2026-07-03
status: seedling
tags:
- gcp
- cloud-build
- ci-cd
- gotcha
title: Cloud Build $COMMIT_SHA is the full 40-char git SHA, not the short one
type: lesson
---

# Cloud Build $COMMIT_SHA is the full 40-char git SHA, not the short one

The `$COMMIT_SHA` substitution Cloud Build injects into a build is the **full 40-character** git commit SHA, not the abbreviated 7-char form. So images tagged `...:$COMMIT_SHA` in the pipeline land in the registry under the full SHA.

**Consequence:** a hand-rolled deploy script that resolves the image tag with `git rev-parse --short HEAD` builds a tag that does not exist in Artifact Registry, and the rollout fails with `ImagePullBackOff` / `ErrImagePull`. Use `git rev-parse HEAD` (full) to match Cloud Build's convention.

Verify what actually exists with `gcloud artifacts docker tags list <repo-path>` before deploying.

## Related

- [[GKE Immediate-binding StorageClass deadlocks a single-replica StatefulSet across zones]]

%% ai-graph-start %%

**Related notes:**
- [[kubectl rollout status timeout must cover cold image pull time]]
- [[Cloud Build treats $VAR in step args as its own substitution; escape shell $ as $$]]
- [[GKE Immediate-binding StorageClass deadlocks a single-replica StatefulSet across zones]]
- [[Cloud Run can only pull images from Artifact Registry or GCR, not GHCR]]
- [[Rollout restart uses the LIVE spec - a manifest edited only in git changes nothing]]

%% ai-graph-end %%