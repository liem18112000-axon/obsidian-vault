---
title: "gcloud builds submit log-streaming error is not a build failure (use --suppress-logs)"
created: 2026-08-27
type: lesson
status: seedling
source: "session 2026-08-27 — kga"
tags: [cloud-build, gcloud, gcp, vpc-sc, gotcha]
---

# gcloud builds submit log-streaming error is not a build failure (use --suppress-logs)

gcloud builds submit can exit with `ERROR: ... can only stream logs if you are Viewer/Owner of the project and ... VPC-SC` even though **the build itself submits and runs fine** server-side. The error is ONLY about streaming logs from the default logs bucket (which lives outside any VPC-SC perimeter); it is not a build failure.

Fixes:
- Add **`--suppress-logs`** — gcloud stops streaming, still waits for the build, and reports the final status (exit code reflects real success/failure). Best for CI/low-privilege callers.
- Or **`--async`** — submit and return immediately with a build id; poll with `gcloud builds describe <id> --region=<region> --format="value(status)"`.
- Or grant the caller `roles/viewer` (or use `--gcs-log-dir` pointing at a bucket you own, e.g. inside the VPC-SC perimeter).

Check a build without streaming: `gcloud builds describe <BUILD_ID> --region=global --format="value(status)"` → SUCCESS/WORKING/FAILURE. The submit output prints the build id + region even when streaming is denied.

Context: kga build-image.sh (LUZ-159671 test-agent).
