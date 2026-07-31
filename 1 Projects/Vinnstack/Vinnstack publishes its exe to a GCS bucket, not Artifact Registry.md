---
title: "Vinnstack publishes its exe to a GCS bucket, not Artifact Registry"
created: 2026-07-07
type: observation
status: seedling
source: "vinnstack cloudbuild.yaml setup, 2026-07-07"
tags: [vinnstack, cloud-build, gcs, iam, decision]
---

# Vinnstack publishes its exe to a GCS bucket, not Artifact Registry

Vinnstack'\''s Cloud Build pipeline publishes the packaged `vinnstack.exe` to a plain GCS bucket (`gs://vinnstack-exe-release`, project `klara-nonprod`) rather than an Artifact Registry generic repo, even though a generic Artifact Registry repo (`generic-artifact`) already existed for this purpose.

The switch happened mid-setup because the operator'\''s gcloud account could grant `roles/storage.objectAdmin` on the bucket to the Cloud Build service account (`cloudbuild-bitbucket-sa@klara-infra.iam.gserviceaccount.com`) but could **not** grant `roles/artifactregistry.writer` on the Artifact Registry repo — see [[artifactregistry.repoAdmin cannot grant IAM despite the name]]. Rather than block the whole task on someone else applying the Artifact Registry grant, the destination was changed to the one the operator could actually finish wiring end-to-end.

Objects are written to `gs://vinnstack-exe-release/<short-sha>/vinnstack.exe` (immutable, one per commit) and `gs://vinnstack-exe-release/latest/vinnstack.exe` (overwritten every build) — see [[Publish a single file to GCS from Cloud Build with gcloud storage cp]].

## Related
[[Vinnstack Cloud Build trigger lives in klara-infra, not klara-nonprod]]
[[artifactregistry.repoAdmin cannot grant IAM despite the name]]
