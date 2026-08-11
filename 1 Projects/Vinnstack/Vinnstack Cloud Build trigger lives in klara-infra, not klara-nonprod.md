---
ai_hash: 6a865f299de770ff
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-07
entities:
- Vinnstack
- Vinnstack Cloud Build trigger
- klara-infra
- klara-nonprod
- Cloud SQL instance
- Vinnstack Artifact Registry repo
- europe-west6
- bitbucket-axonivy-prod-connection
- Bitbucket Cloud
- main-public branch
- main branch
- cloudbuild.yaml
- repo root
- cloudbuild-bitbucket-sa@klara-infra.iam.gserviceaccount.com
- Cloud Build service account
- IAM grant
- Vinnstack bundles cloud-sql-proxy.exe as a gitignored extraResource (Note)
- Publish arbitrary binaries to Artifact Registry with a generic repo (Note)
- cloud-sql-proxy.exe
- extraResource
- Generic Artifact Registry repo
source: vinnstack cloudbuild.yaml setup, 2026-07-07
status: seedling
tags:
- vinnstack
- cloud-build
- klara-infra
- klara-nonprod
- iam
title: Vinnstack Cloud Build trigger lives in klara-infra, not klara-nonprod
type: observation
---

# Vinnstack Cloud Build trigger lives in klara-infra, not klara-nonprod

Vinnstack'\''s Cloud Build trigger (name `vinnstack`) does **not** live in `klara-nonprod` (the project vinnstack'\''s own cloud resources, like its Cloud SQL instance and its generic Artifact Registry repo, live in) — it lives in **`klara-infra`**, region `europe-west6`, alongside triggers for the other axonivy-prod repos.

It'\''s connected via the `bitbucket-axonivy-prod-connection` Bitbucket Cloud connection, fires on push to branch `main-public` (not `main`), runs `cloudbuild.yaml` from the repo root, and executes as service account `cloudbuild-bitbucket-sa@klara-infra.iam.gserviceaccount.com`.

Because the build runs under a `klara-infra`-scoped service account, any step that needs to touch resources in another project (e.g. uploading an artifact to `klara-nonprod`'\''s Artifact Registry) requires an explicit cross-project IAM grant to that SA on the target resource — it has no implicit access just because the build is "for" that other project.

## Related
[[Vinnstack bundles cloud-sql-proxy.exe as a gitignored extraResource]]
[[Publish arbitrary binaries to Artifact Registry with a generic repo]]

%% ai-graph-start %%

**Related notes:**
- [[Vinnstack publishes its exe to a GCS bucket, not Artifact Registry]]
- [[Klara Cloud Build pushes images to klara-repo Artifact Registry with the SA on the trigger]]
- [[Vinnstack release push to main triggers Cloud Build which publishes to GCS latest auto-update channel]]
- [[klara-nonprod is the GCP project for non-prod Artifact Registry IAM]]
- [[Vinnstack bundles cloud-sql-proxy.exe as a gitignored extraResource]]

**Relations:**
- Vinnstack — *has* — Vinnstack Cloud Build trigger
- Vinnstack Cloud Build trigger — *lives in project* — klara-infra
- Vinnstack Cloud Build trigger — *is not in project* — klara-nonprod
- Vinnstack — *has cloud resources in project* — klara-nonprod
- klara-nonprod — *hosts* — Cloud SQL instance
- klara-nonprod — *hosts* — Vinnstack Artifact Registry repo
- klara-infra — *region is* — europe-west6
- Vinnstack Cloud Build trigger — *uses connection* — bitbucket-axonivy-prod-connection
- bitbucket-axonivy-prod-connection — *is type* — Bitbucket Cloud
- Vinnstack Cloud Build trigger — *fires on push to* — main-public branch
- Vinnstack Cloud Build trigger — *does not fire on push to* — main branch
- Vinnstack Cloud Build trigger — *runs config file* — cloudbuild.yaml
- cloudbuild.yaml — *is located at* — repo root
- Vinnstack Cloud Build trigger — *executes as* — cloudbuild-bitbucket-sa@klara-infra.iam.gserviceaccount.com
- cloudbuild-bitbucket-sa@klara-infra.iam.gserviceaccount.com — *is a* — Cloud Build service account
- Cloud Build service account — *is scoped to project* — klara-infra
- uploading artifact to — *requires* — IAM grant
- IAM grant — *is for service account* — cloudbuild-bitbucket-sa@klara-infra.iam.gserviceaccount.com
- Vinnstack — *related to note* — Vinnstack bundles cloud-sql-proxy.exe as a gitignored extraResource (Note)
- Vinnstack Artifact Registry repo — *related to note* — Publish arbitrary binaries to Artifact Registry with a generic repo (Note)
- Vinnstack bundles cloud-sql-proxy.exe as a gitignored extraResource (Note) — *mentions* — cloud-sql-proxy.exe
- Vinnstack bundles cloud-sql-proxy.exe as a gitignored extraResource (Note) — *mentions* — extraResource
- Publish arbitrary binaries to Artifact Registry with a generic repo (Note) — *mentions* — Generic Artifact Registry repo

%% ai-graph-end %%