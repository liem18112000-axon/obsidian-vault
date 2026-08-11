---
ai_hash: 8fd7aa8d994b2bbd
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-08
entities: []
source: vinnstack build optimization session
status: seedling
tags:
- cloud-build
- ci-cd
- gcp
- pipeline-optimization
title: Google Cloud Build steps share one workspace volume across images
type: lesson
---

# Google Cloud Build steps share one workspace volume across images

In Google Cloud Build, every step in a `cloudbuild.yaml` mounts the same `/workspace` volume, regardless of which container image each step uses. Files written by an earlier step (e.g. `node_modules` from `npm ci`, or a `.next` build output from `npm run build`) are still there when a later step runs, even if that step uses a completely different image.

Two consequences worth designing pipelines around:
1. Re-running an expensive setup command (like `npm ci`) in every step that needs its output is pure waste if an earlier step already did it — just add that step to `waitFor` instead.
2. Steps only run sequentially because of explicit `waitFor` dependencies — Cloud Build parallelizes any steps whose `waitFor` lists don't force an order (a DAG, not a linear list). Splitting a pipeline into narrower steps (e.g. a test step and a build step that both only depend on install, not on each other) lets Cloud Build run them concurrently and cuts wall-clock time.

Applied this to a Vinnstack (Electron) pipeline: `npm ci` was being run twice (once in a test step, once in a separate wine-image build step) — collapsed to one `install` step, then split `test` and `next build` into parallel branches off of it, with only the final electron-builder packaging step (which needs wine for the Windows cross-build) waiting on both.

%% ai-graph-start %%

**Related notes:**
- [[Vinnstack release push to main triggers Cloud Build which publishes to GCS latest auto-update channel]]
- [[Diagnose Cloud Build failures with gcloud builds describe and log]]
- [[Publish a single file to GCS from Cloud Build with gcloud storage cp]]
- [[Cross-building Electron Windows exe on Linux needs wine]]
- [[gcloud components install fetches the host's own platform binary, not a chosen target]]

%% ai-graph-end %%