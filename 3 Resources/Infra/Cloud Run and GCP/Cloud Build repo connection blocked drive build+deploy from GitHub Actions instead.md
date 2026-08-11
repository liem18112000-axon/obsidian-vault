---
ai_hash: 46b5b443d4433bcc
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-01
entities: []
source: session 2026-06-01, vault-graph
status: seedling
tags:
- gcp
- cloud-build
- github-actions
- cloud-run
- cicd
- gotcha
title: 'Cloud Build repo connection blocked: drive build+deploy from GitHub Actions
  instead'
type: lesson
---

# Cloud Build repo connection blocked: drive build+deploy from GitHub Actions instead

When you cannot create a Google Cloud Build **2nd-gen GitHub repo connection** — typically because the console OAuth flow needs the Cloud Build GitHub App installed on the GitHub **organization**, which requires org-owner approval you may not have — drive the identical build+deploy pipeline from **GitHub Actions** instead. A GitHub Actions workflow needs only a plain **repo secret**, which requires no OAuth and no org-level App install, so it bypasses exactly what blocks the Cloud Build connection.

## How it works
The workflow authenticates with `google-github-actions/auth@v2` using a `GCP_SA_KEY` repo secret (a CI service-account JSON key), then:
1. `docker build` the image (and tag `:$SHA` + `:latest`),
2. `docker push` to Artifact Registry (after `gcloud auth configure-docker <region>-docker.pkg.dev`),
3. `gcloud run deploy <svc> --image=<img> --region=<r> --quiet` — **image-only**, so Cloud Run preserves the service's existing service account, ingress (`--allow-unauthenticated`), and env vars from the bootstrap deploy. No secrets live in the workflow file.

## Why it is often *better*, not just a fallback
- The GitHub Actions run logs are visible in the GitHub UI, so you do not need GCP **Viewer** / Cloud Build log-streaming permission (which a borrowed deploy account may lack).
- One identity + one key (`GCP_SA_KEY`) can serve several repos/pipelines.

The CI SA still needs the right IAM — see [[IAM roles a CI service account needs to build and deploy to Cloud Run]].

Context discovered: vault-graph project, Cloud Run, `klara-nonprod`. The same repo's vault-enrichment pipeline (in the `obsidian-vault` repo) already used GitHub Actions + `GCP_SA_KEY`, so this just made both pipelines consistent.

## Related

- [[IAM roles a CI service account needs to build and deploy to Cloud Run]]

%% ai-graph-start %%

**Related notes:**
- [[IAM roles a CI service account needs to build and deploy to Cloud Run]]
- [[GCP auth ambient ADC in GCP-hosted runners vs explicit creds in external CI]]
- [[Cloud Run can only pull images from Artifact Registry or GCR, not GHCR]]
- [[Set up GitHub Actions to GCP via Workload Identity Federation]]
- [[Pipe a GCP service-account key straight into a GitHub secret without leaking it]]

%% ai-graph-end %%