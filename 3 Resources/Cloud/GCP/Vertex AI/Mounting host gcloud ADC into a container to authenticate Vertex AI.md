---
ai_hash: 37b7a0db60464b2b
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-14
entities: []
source: Accesstrade integration, session 2026-06-13
status: seedling
tags:
- vertex-ai
- docker
- adc
- gcp
- authentication
title: Mounting host gcloud ADC into a container to authenticate Vertex AI
type: howto
---

# Mounting host gcloud ADC into a container to authenticate Vertex AI

Application Default Credentials (ADC) are **not** automatically available inside a Docker container — code using google-genai / Vertex AI in a container will fail to authenticate unless you supply credentials explicitly. The simplest fix for local/dev: **mount the host gcloud ADC file read-only** and point `GOOGLE_APPLICATION_CREDENTIALS` at the mount.

In `docker-compose.yml`:

```yaml
environment:
  GOOGLE_APPLICATION_CREDENTIALS: /gcloud/adc.json
volumes:
  - ${GOOGLE_ADC:-${APPDATA}/gcloud/application_default_credentials.json}:/gcloud/adc.json:ro
```

Host ADC file location:
- **Windows:** `%APPDATA%\gcloud\application_default_credentials.json`
- **Linux/macOS:** `~/.config/gcloud/application_default_credentials.json`

The file is produced by `gcloud auth application-default login` and carries a `quota_project_id`. The `${GOOGLE_ADC:-...}` form lets an env override the path while defaulting to the Windows gcloud location. For production prefer a mounted service-account key or workload identity over a developer ADC file.

Context: enabling Vertex in the Accesstrade web container. Related: [[google-genai Client must be held in a variable during the request or it is GC-closed]].

## Related

- [[google-genai Client must be held in a variable during the request or it is GC-closed]]

%% ai-graph-start %%

**Related notes:**
- [[Non-WI GKE Google API auth mount a GSA key at the well-known ADC path]]
- [[Claude Code runs on Vertex AI via three env vars with gcloud ADC]]
- [[google-genai Client must be held in a variable during the request or it is GC-closed]]
- [[GCP auth ambient ADC in GCP-hosted runners vs explicit creds in external CI]]
- [[List Anthropic models on Vertex via the publisherModels REST endpoint]]

%% ai-graph-end %%