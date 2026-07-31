---
title: "GCP auth: ambient ADC in GCP-hosted runners vs explicit creds in external CI"
created: 2026-06-14
type: lesson
status: seedling
source: "session 2026-06-14, luz_docs_integration_test analysis"
tags: [gcp, authentication, adc, cloud-build, github-actions, workload-identity, ci-cd]
---

# GCP auth: ambient ADC in GCP-hosted runners vs explicit creds in external CI

**The `google-github-actions/auth failed: must specify exactly one of workload_identity_provider or credentials_json` error happens because GitHub Actions runs OUTSIDE Google Cloud — there is no ambient identity, so you must hand the auth action exactly one credential. Code running INSIDE GCP (Cloud Build, Cloud Run, GKE, a GCE VM) needs none of that: the attached service account IS the credential, picked up automatically via Application Default Credentials (ADC).**

Observed in luz_docs_integration_test: all GCP work runs in **Google Cloud Build** and just calls `gcloud container clusters get-credentials ...` / `kubectl` with ZERO explicit creds — no auth action, no key file, no WIF inputs. It works because the Cloud Build runner already runs as a GCP service account. Same for its Vertex code: `google.auth.default(scopes=[cloud-platform])` and `genai.Client(vertexai=True, project=..., location=...)` resolve ADC automatically. The only real secret (a Slack webhook) comes from **Secret Manager** via `availableSecrets.secretManager` + `secretEnv`, not from CI inputs.

So, two ways to 'work with Google without that error':
1. **Run the GCP step where ADC is ambient** (Cloud Build / Cloud Run / GKE / GCE). No auth action, no keys, no secrets to inject. This is the luz approach and it structurally cannot throw the 'exactly one' error because there's no auth action.
2. **If you must auth from GitHub Actions** (external): provide exactly ONE of —
   - Workload Identity Federation: `workload_identity_provider` + `service_account` (job needs `permissions: id-token: write`); one-time GCP setup binds a WIF pool/provider to the repo. No long-lived key.
   - a service-account key JSON in `credentials_json`.
   The error = BOTH were empty (unset secrets). Also: secrets are NOT passed to fork/Dependabot PRs, so those runs see empty creds — gate/skip them (see [[Gate a GitHub Actions job on secret presence via a preflight job output]]).

Rule of thumb: pick the runner to match the cloud. GCP work → run it in GCP (free ADC). Only reach for WIF/keys when the runner lives outside GCP. Relates to [[Publish a Docker image to GHCR from GitHub Actions with GITHUB_TOKEN]] (the GHCR analogue: GITHUB_TOKEN is the ambient identity inside GitHub Actions).
