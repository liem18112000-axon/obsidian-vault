---
title: "GCP APIs must be enabled individually per project"
created: 2026-07-11
type: lesson
status: seedling
source: "virtual-avatar project, 2026-07-11"
tags: [gcp, gcloud, troubleshooting, permission-denied]
---

# GCP APIs must be enabled individually per project

Enabling one Google Cloud API on a project (e.g. Cloud Text-to-Speech) does not enable a related-sounding one (e.g. Cloud Speech-to-Text) — each API is gated independently per GCP project. A client library call against a disabled API fails with a gRPC `PERMISSION_DENIED`/`SERVICE_DISABLED` error whose message names the specific API and gives a console activation URL — this is a project-configuration gap, not a credentials or code bug, even though the error surfaces as a generic-looking 403/PERMISSION_DENIED deep in a stack trace.

Fix: `gcloud services enable <api>.googleapis.com --project <project-id>`. Before assuming an auth/IAM problem, check what's actually enabled with `gcloud services list --enabled --project <project-id> | grep <api>` — it's a fast way to rule this out first.
