---
ai_hash: 4392e0ce20c516e5
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-11
entities: []
source: virtual-avatar project, 2026-07-11
status: seedling
tags:
- gcp
- gcloud
- troubleshooting
- permission-denied
title: GCP APIs must be enabled individually per project
type: lesson
---

# GCP APIs must be enabled individually per project

Enabling one Google Cloud API on a project (e.g. Cloud Text-to-Speech) does not enable a related-sounding one (e.g. Cloud Speech-to-Text) — each API is gated independently per GCP project. A client library call against a disabled API fails with a gRPC `PERMISSION_DENIED`/`SERVICE_DISABLED` error whose message names the specific API and gives a console activation URL — this is a project-configuration gap, not a credentials or code bug, even though the error surfaces as a generic-looking 403/PERMISSION_DENIED deep in a stack trace.

Fix: `gcloud services enable <api>.googleapis.com --project <project-id>`. Before assuming an auth/IAM problem, check what's actually enabled with `gcloud services list --enabled --project <project-id> | grep <api>` — it's a fast way to rule this out first.

%% ai-graph-start %%

**Related notes:**
- [[Diagnose GCP console permission errors with the testIamPermissions REST probe]]
- [[Vertex AI 404 vs 403 distinguishes Model Garden enablement from IAM permission]]
- [[Google Cloud TTS from Windows fetch the token in bash, pass via env to Python]]
- [[Cloud SQL Auth Proxy needs roles-cloudsql.client on the connecting identity or it 403s NOT_AUTHORIZED]]
- [[Vertex models.list() shows the catalog, not what the project can invoke; Gemini 3 needs the global endpoint]]

%% ai-graph-end %%