---
title: "IAM roles a CI service account needs to build and deploy to Cloud Run"
created: 2026-06-01
type: concept
status: seedling
source: "session 2026-06-01, vault-graph"
tags: [gcp, iam, cloud-run, artifact-registry, service-account, cicd]
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
