---
ai_hash: 9191955029d62f4e
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-01
entities: []
source: session 2026-06-01, vault-graph
status: seedling
tags:
- gcp
- iam
- cloud-run
- artifact-registry
- service-account
- cicd
title: IAM roles a CI service account needs to build and deploy to Cloud Run
type: concept
---

# IAM roles a CI service account needs to build and deploy to Cloud Run

A CI service account that builds a container image and deploys it to **Cloud Run** needs exactly three IAM grants. Anything less fails at a predictable step.

| Grant | Resource scope | Why |
|---|---|---|
| `roles/cloudbuild.builds.builder` | project | Despite the name, this role includes `artifactregistry.repositories.uploadArtifacts` (+ `tags.create`, `downloadArtifacts`), so it covers the **`docker push` to Artifact Registry** even when you build in a GitHub Actions runner rather than in Cloud Build. |
| `roles/run.developer` | project | Lets the SA run `gcloud run deploy` (create/update the service + revision). |
| `roles/iam.serviceAccountUser` (actAs) | on the **runtime** SA | Cloud Run runs the service *as* a runtime SA; to deploy a service that uses that SA, the deployer must have `actAs` **on that SA resource** (not project-wide). Without it, deploy fails with a `iam.serviceaccounts.actAs` permission error. |

## Notes
- The `actAs` binding is on the *runtime SA as a resource*, so it will **not** appear in `gcloud projects get-iam-policy` — check it with `gcloud iam service-accounts get-iam-policy <runtime-sa>`.
- Keep the **runtime** SA minimal (it is attached to a possibly-public service); put the build/deploy power on a **separate CI SA**. Never reuse the runtime SA for deploys.
- Verify a role actually contains a permission with: `gcloud iam roles describe roles/cloudbuild.builds.builder --format="value(includedPermissions)"`.

Used by the GitHub-Actions deploy pattern in [[Cloud Build repo connection blocked drive build+deploy from GitHub Actions instead]]. Context: `vault-graph-ci@klara-nonprod`.

## Related

- [[Cloud Build repo connection blocked drive build+deploy from GitHub Actions instead]]

%% ai-graph-start %%

**Related notes:**
- [[Cloud Build repo connection blocked drive build+deploy from GitHub Actions instead]]
- [[Cloud Run can only pull images from Artifact Registry or GCR, not GHCR]]
- [[Klara Cloud Build pushes images to klara-repo Artifact Registry with the SA on the trigger]]
- [[GCP auth ambient ADC in GCP-hosted runners vs explicit creds in external CI]]
- [[klara-nonprod is the GCP project for non-prod Artifact Registry IAM]]

%% ai-graph-end %%