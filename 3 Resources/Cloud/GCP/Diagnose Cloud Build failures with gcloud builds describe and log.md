---
title: "Diagnose Cloud Build failures with gcloud builds describe and log"
created: 2026-07-26
type: howto
status: seedling
source: "vinnstack cloud-build fix, session 2026-07-26"
tags: [gcloud, cloud-build, ci, debugging]
---

# Diagnose Cloud Build failures with gcloud builds describe and log

To diagnose a Google Cloud Build failure when you only have the console URL (which you cannot open headless), drive it from the `gcloud` CLI. The build id, region, and project are all in the URL.

- Per-step status + exit codes: `gcloud builds describe <BUILD_ID> --region=<region> --project=<project> --format=json` (inspect `steps[].status` / `steps[].exitCode`).
- Full log output: `gcloud builds log <BUILD_ID> --region=<region> --project=<project>`.

Reading it: the step with `status: FAILURE` and `exitCode: 1` is the real culprit. Sibling steps that ran in parallel (same `waitFor`) show `status: CANCELLED` — that is NOT a failure of those steps, just Cloud Build aborting them once a sibling failed. Filter noisy apt-get/install output and grep the test-runner summary lines (`Test Files`, `Tests`, `FAIL`).

Real case (vinnstack, 2026-07): step `test` FAILURE exitCode 1 (`npm run test`); sibling `build` showed CANCELLED and downstream `package-exe`/`upload-artifact` QUEUED — only `test` was actually broken.
