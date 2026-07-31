---
title: "klara-nonprod is the GCP project for non-prod Artifact Registry IAM"
created: 2026-07-07
type: observation
status: seedling
source: "session 2026-07-07"
tags: [gcp, iam, artifact-registry, klara]
---

# klara-nonprod is the GCP project for non-prod Artifact Registry IAM

For this org, the GCP project used for non-prod Artifact Registry repos and their IAM grants is `klara-nonprod` — not `klara-repo`.

`klara-repo` looked like the plausible project name (it appears as a default in some deploy skills, e.g. Cloud Build triggers), but attempting to grant IAM roles there failed with a permission error for the active account, while the same grant succeeded immediately on `klara-nonprod`. When granting Artifact Registry permissions (e.g. `roles/artifactregistry.repoAdmin` for `artifactregistry.repositories.create`), target `klara-nonprod` first for non-prod work.

## Related

- [[gcloud add-iam-policy-binding requires --condition=None when policy has conditional bindings]]
