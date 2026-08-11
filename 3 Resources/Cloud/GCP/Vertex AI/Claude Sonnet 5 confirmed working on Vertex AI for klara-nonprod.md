---
ai_hash: 9f40593e24b9fe61
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-11
entities: []
source: session 2026-07-11
status: seedling
tags:
- gcp
- vertex-ai
- klara-nonprod
- claude
title: Claude Sonnet 5 confirmed working on Vertex AI for klara-nonprod
type: observation
---

# Claude Sonnet 5 confirmed working on Vertex AI for klara-nonprod

As of 2026-07-11, Claude **Sonnet 5** (`claude-sonnet-5`, bare model ID with no `@date` suffix) is enabled and working on Vertex AI for the GCP project `klara-nonprod`, using the **global** endpoint only:

```
https://aiplatform.googleapis.com/v1/projects/klara-nonprod/locations/global/publishers/anthropic/models/claude-sonnet-5:rawPredict
```

A real `rawPredict` call returned `HTTP 200` with a valid Claude response. Getting here required, in order: (1) granting the account `roles/consumerprocurement.entitlementManager` on the project, (2) enabling the `claude-sonnet-5` model card in Vertex AI Model Garden (accepting Anthropic's terms), which resolved the earlier 404s, and (3) working past an initial `429` zero-quota response — quota turned out to already be sufficient on the global endpoint by the time of the successful call.

Other Claude models (e.g. `claude-haiku-4-5`) are **not** enabled on this project — only Sonnet 5 was explicitly enabled. `klara-nonprod` is one of several GCP projects in the "klara" org (alongside `klara-infra`, `klara-performance`, `klara-prod`, `klara-repo`).

See [[Vertex AI global endpoint host has no region prefix]] and [[Vertex AI Model Garden enablement and quota are separate, per-model steps]] for the mechanics behind getting this working.

## Related

- [[Vertex AI global endpoint host has no region prefix]]
- [[Vertex AI Model Garden enablement and quota are separate, per-model steps]]

%% ai-graph-start %%

**Related notes:**
- [[Vertex AI Model Garden enablement and quota are separate, per-model steps]]
- [[Vertex AI global endpoint host has no region prefix]]
- [[Claude models are available on GCP Vertex AI Model Garden]]
- [[List Anthropic models on Vertex via the publisherModels REST endpoint]]
- [[Claude Code runs on Vertex AI via three env vars with gcloud ADC]]

%% ai-graph-end %%