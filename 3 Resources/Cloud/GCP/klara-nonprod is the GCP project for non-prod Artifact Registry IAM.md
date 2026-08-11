---
ai_hash: 3567867926c1aefa
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-07
entities: []
source: session 2026-07-07
status: seedling
tags:
- gcp
- iam
- artifact-registry
- klara
title: klara-nonprod is the GCP project for non-prod Artifact Registry IAM
type: observation
---

# klara-nonprod is the GCP project for non-prod Artifact Registry IAM

For this org, the GCP project used for non-prod Artifact Registry repos and their IAM grants is `klara-nonprod` — not `klara-repo`.

`klara-repo` looked like the plausible project name (it appears as a default in some deploy skills, e.g. Cloud Build triggers), but attempting to grant IAM roles there failed with a permission error for the active account, while the same grant succeeded immediately on `klara-nonprod`. When granting Artifact Registry permissions (e.g. `roles/artifactregistry.repoAdmin` for `artifactregistry.repositories.create`), target `klara-nonprod` first for non-prod work.

## Related

- [[gcloud add-iam-policy-binding requires --condition=None when policy has conditional bindings]]

%% ai-graph-start %%

**Related notes:**
- [[gcloud add-iam-policy-binding requires --condition=None when policy has conditional bindings]]
- [[Klara Cloud Build pushes images to klara-repo Artifact Registry with the SA on the trigger]]
- [[Vinnstack Cloud Build trigger lives in klara-infra, not klara-nonprod]]
- [[klara-prod is a separate GCP project, not a namespace]]
- [[artifactregistry.repoAdmin cannot grant IAM despite the name]]

%% ai-graph-end %%