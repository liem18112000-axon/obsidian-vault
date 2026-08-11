---
ai_hash: 90a62bde7a0f7945
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-07
entities:
- Vinnstack
- vinnstack.exe
- GCS bucket
- Artifact Registry
- Cloud Build pipeline
- gs://vinnstack-exe-release
- klara-nonprod
- generic-artifact
- roles/storage.objectAdmin
- cloudbuild-bitbucket-sa@klara-infra.iam.gserviceaccount.com
- roles/artifactregistry.writer
- artifactregistry.repoAdmin cannot grant IAM despite the name
- gs://vinnstack-exe-release/<short-sha>/vinnstack.exe
- gs://vinnstack-exe-release/latest/vinnstack.exe
- Publish a single file to GCS from Cloud Build with gcloud storage cp
- Vinnstack Cloud Build trigger lives in klara-infra, not klara-nonprod
source: vinnstack cloudbuild.yaml setup, 2026-07-07
status: seedling
tags:
- vinnstack
- cloud-build
- gcs
- iam
- decision
title: Vinnstack publishes its exe to a GCS bucket, not Artifact Registry
type: observation
---

# Vinnstack publishes its exe to a GCS bucket, not Artifact Registry

Vinnstack'\''s Cloud Build pipeline publishes the packaged `vinnstack.exe` to a plain GCS bucket (`gs://vinnstack-exe-release`, project `klara-nonprod`) rather than an Artifact Registry generic repo, even though a generic Artifact Registry repo (`generic-artifact`) already existed for this purpose.

The switch happened mid-setup because the operator'\''s gcloud account could grant `roles/storage.objectAdmin` on the bucket to the Cloud Build service account (`cloudbuild-bitbucket-sa@klara-infra.iam.gserviceaccount.com`) but could **not** grant `roles/artifactregistry.writer` on the Artifact Registry repo — see [[artifactregistry.repoAdmin cannot grant IAM despite the name]]. Rather than block the whole task on someone else applying the Artifact Registry grant, the destination was changed to the one the operator could actually finish wiring end-to-end.

Objects are written to `gs://vinnstack-exe-release/<short-sha>/vinnstack.exe` (immutable, one per commit) and `gs://vinnstack-exe-release/latest/vinnstack.exe` (overwritten every build) — see [[Publish a single file to GCS from Cloud Build with gcloud storage cp]].

## Related
[[Vinnstack Cloud Build trigger lives in klara-infra, not klara-nonprod]]
[[artifactregistry.repoAdmin cannot grant IAM despite the name]]

%% ai-graph-start %%

**Related notes:**
- [[Vinnstack Cloud Build trigger lives in klara-infra, not klara-nonprod]]
- [[Vinnstack release push to main triggers Cloud Build which publishes to GCS latest auto-update channel]]
- [[Publish a single file to GCS from Cloud Build with gcloud storage cp]]
- [[Publish arbitrary binaries to Artifact Registry with a generic repo]]
- [[artifactregistry.repoAdmin cannot grant IAM despite the name]]

**Relations:**
- Vinnstack — *uses* — Cloud Build pipeline
- Cloud Build pipeline — *publishes* — vinnstack.exe
- vinnstack.exe — *is published to* — GCS bucket
- GCS bucket — *is named* — gs://vinnstack-exe-release
- gs://vinnstack-exe-release — *is in project* — klara-nonprod
- Artifact Registry — *has repo* — generic-artifact
- generic-artifact — *was intended for* — vinnstack.exe
- roles/storage.objectAdmin — *granted to* — cloudbuild-bitbucket-sa@klara-infra.iam.gserviceaccount.com
- cloudbuild-bitbucket-sa@klara-infra.iam.gserviceaccount.com — *is a* — Cloud Build service account
- roles/artifactregistry.writer — *could not be granted to* — cloudbuild-bitbucket-sa@klara-infra.iam.gserviceaccount.com
- artifactregistry.repoAdmin cannot grant IAM despite the name — *explains issue with* — roles/artifactregistry.writer
- vinnstack.exe — *is stored at* — gs://vinnstack-exe-release/<short-sha>/vinnstack.exe
- vinnstack.exe — *is stored at* — gs://vinnstack-exe-release/latest/vinnstack.exe
- gs://vinnstack-exe-release/<short-sha>/vinnstack.exe — *is* — immutable
- gs://vinnstack-exe-release/latest/vinnstack.exe — *is* — overwritten
- Publish a single file to GCS from Cloud Build with gcloud storage cp — *describes method for* — publishing to GCS bucket
- Vinnstack Cloud Build trigger lives in klara-infra, not klara-nonprod — *is related to* — Vinnstack
- artifactregistry.repoAdmin cannot grant IAM despite the name — *is related to* — Artifact Registry

%% ai-graph-end %%