---
title: "Publish a single file to GCS from Cloud Build with gcloud storage cp"
created: 2026-07-07
type: howto
status: seedling
source: "vinnstack cloudbuild.yaml setup, 2026-07-07"
tags: [gcp, gcs, cloud-build, ci]
---

# Publish a single file to GCS from Cloud Build with gcloud storage cp

To publish a single arbitrary file from a Cloud Build step to Google Cloud Storage, no bucket-specific tooling is needed — just run `gcloud storage cp <local-path> gs://<bucket>/<path>` inside a step using the `gcr.io/google.com/cloudsdktool/cloud-sdk:slim` image.

A useful versioning convention (mirrors the tag convention for container images): write the file to both `gs://<bucket>/${SHORT_SHA}/<file>` (an immutable copy traceable back to the exact commit, using Cloud Build'\''s built-in `$SHORT_SHA` substitution) and `gs://<bucket>/latest/<file>` (overwritten every build, giving a stable download link for whoever just wants "the current build").

## Related
[[Vinnstack publishes its exe to a GCS bucket, not Artifact Registry]]
[[Uniform bucket-level access means GCS permissions are IAM-only]]
[[Publish arbitrary binaries to Artifact Registry with a generic repo]]
