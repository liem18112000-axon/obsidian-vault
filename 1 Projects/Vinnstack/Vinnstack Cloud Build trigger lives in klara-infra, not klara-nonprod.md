---
title: "Vinnstack Cloud Build trigger lives in klara-infra, not klara-nonprod"
created: 2026-07-07
type: observation
status: seedling
source: "vinnstack cloudbuild.yaml setup, 2026-07-07"
tags: [vinnstack, cloud-build, klara-infra, klara-nonprod, iam]
---

# Vinnstack Cloud Build trigger lives in klara-infra, not klara-nonprod

Vinnstack'\''s Cloud Build trigger (name `vinnstack`) does **not** live in `klara-nonprod` (the project vinnstack'\''s own cloud resources, like its Cloud SQL instance and its generic Artifact Registry repo, live in) — it lives in **`klara-infra`**, region `europe-west6`, alongside triggers for the other axonivy-prod repos.

It'\''s connected via the `bitbucket-axonivy-prod-connection` Bitbucket Cloud connection, fires on push to branch `main-public` (not `main`), runs `cloudbuild.yaml` from the repo root, and executes as service account `cloudbuild-bitbucket-sa@klara-infra.iam.gserviceaccount.com`.

Because the build runs under a `klara-infra`-scoped service account, any step that needs to touch resources in another project (e.g. uploading an artifact to `klara-nonprod`'\''s Artifact Registry) requires an explicit cross-project IAM grant to that SA on the target resource — it has no implicit access just because the build is "for" that other project.

## Related
[[Vinnstack bundles cloud-sql-proxy.exe as a gitignored extraResource]]
[[Publish arbitrary binaries to Artifact Registry with a generic repo]]
