---
ai_hash: a620495dcaa4e05e
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
entities: []
tags:
- gcp
- cloud-build
- artifact-registry
- klara
- kepler
- ci
---

# Klara Cloud Build pushes images to klara-repo Artifact Registry with the SA on the trigger

Convention across klara/Kepler repos (e.g. luz_docs, vinnstack) for Google Cloud Build:

- **Push container images to:** `europe-west6-docker.pkg.dev/klara-repo/artifact-registry-container-images/<image>:$COMMIT_SHA` (region `europe-west6`, registry project `klara-repo`, repo `artifact-registry-container-images`).
- **Prebuilt builder images** live in `europe-west6-docker.pkg.dev/klara-infra/google-cloud-build/...` (e.g. `mvn:3.8-openjdk-17`, `gcloud`).
- **Build service account is NOT declared in `cloudbuild.yaml`** — it's configured on the Cloud Build **trigger**. The tell is `options.logging: CLOUD_LOGGING_ONLY`, which is *required* when a build runs as a non-default SA (a custom SA has no default GCS log bucket). The SA needs `roles/artifactregistry.writer` on klara-repo.
- Secrets (e.g. a Bitbucket repo token to update `luz_kubernetes`) come from **Secret Manager** via `availableSecrets.secretManager` (project number `518364204428`), not env vars.
- The luz-docs pipeline also clones `luz_kubernetes`, runs `update_image_version.sh`, and triggers an integration-test build — **only on master**. Local-first apps (Vinnstack) skip those k8s/deploy steps and just build+push.

**Gotcha:** the SA email is not in the repo — don't grep for it there; it's trigger config in GCP. Runtime SAs found in these repos (`klara-dev-pubsub@...`, `klara-dev-vn-docs-storage@...` in klara-nonprod) are for the *app*, not the build.

Related: [[Next.js standalone Docker image must copy public and .next static next to server.js]].

%% ai-graph-start %%

**Related notes:**
- [[luz-docs Cloud Build pushes an image for every branch but only master updates luz_kubernetes]]
- [[Vinnstack Cloud Build trigger lives in klara-infra, not klara-nonprod]]
- [[klara-nonprod is the GCP project for non-prod Artifact Registry IAM]]
- [[Vinnstack publishes its exe to a GCS bucket, not Artifact Registry]]
- [[KlaraLuz Maven builds resolve dependencies from Google Artifact Registry and require gcloud auth]]

%% ai-graph-end %%