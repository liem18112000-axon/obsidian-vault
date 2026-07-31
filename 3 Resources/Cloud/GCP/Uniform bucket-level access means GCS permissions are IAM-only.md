---
ai_hash: ad9309dd45c2b59e
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-07
entities: []
source: vinnstack cloudbuild.yaml setup, 2026-07-07
status: seedling
tags:
- gcp
- gcs
- iam
title: Uniform bucket-level access means GCS permissions are IAM-only
type: concept
---

# Uniform bucket-level access means GCS permissions are IAM-only

A GCS bucket with uniform bucket-level access enabled drops legacy per-object ACLs entirely — access is governed purely by IAM bindings at the bucket (or project) level.

Practically: to let a service account write objects into such a bucket, grant it a role like `roles/storage.objectAdmin` directly on the bucket with `gcloud storage buckets add-iam-policy-binding gs://<bucket> --member=serviceAccount:<sa> --role=roles/storage.objectAdmin`. There'\''s no per-object ACL fallback to reach for if the IAM grant is missing or insufficient — the bucket-level policy is the only lever.

## Related
[[Publish a single file to GCS from Cloud Build with gcloud storage cp]]

%% ai-graph-start %%

**Related notes:**
- [[Publish a single file to GCS from Cloud Build with gcloud storage cp]]
- [[IAM roles a CI service account needs to build and deploy to Cloud Run]]
- [[Diagnose GCP console permission errors with the testIamPermissions REST probe]]
- [[artifactregistry.repoAdmin cannot grant IAM despite the name]]
- [[Pipe a GCP service-account key straight into a GitHub secret without leaking it]]

%% ai-graph-end %%