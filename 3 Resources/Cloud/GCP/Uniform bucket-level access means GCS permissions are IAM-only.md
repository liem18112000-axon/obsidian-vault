---
title: "Uniform bucket-level access means GCS permissions are IAM-only"
created: 2026-07-07
type: concept
status: seedling
source: "vinnstack cloudbuild.yaml setup, 2026-07-07"
tags: [gcp, gcs, iam]
---

# Uniform bucket-level access means GCS permissions are IAM-only

A GCS bucket with uniform bucket-level access enabled drops legacy per-object ACLs entirely — access is governed purely by IAM bindings at the bucket (or project) level.

Practically: to let a service account write objects into such a bucket, grant it a role like `roles/storage.objectAdmin` directly on the bucket with `gcloud storage buckets add-iam-policy-binding gs://<bucket> --member=serviceAccount:<sa> --role=roles/storage.objectAdmin`. There'\''s no per-object ACL fallback to reach for if the IAM grant is missing or insufficient — the bucket-level policy is the only lever.

## Related
[[Publish a single file to GCS from Cloud Build with gcloud storage cp]]
