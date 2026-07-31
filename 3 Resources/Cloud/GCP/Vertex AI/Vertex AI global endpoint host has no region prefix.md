---
ai_hash: 4b1af22f364d497a
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-11
entities: []
source: sessions 2026-07-11, klara-nonprod Claude on Vertex setup (user-confirmed
  live test)
status: evergreen
tags:
- gcp
- vertex-ai
- gotcha
- api
- rest-api
- claude
title: Vertex AI global endpoint host has no region prefix
type: lesson
---

# Vertex AI global endpoint host has no region prefix

The Vertex AI **global** endpoint is the bare host `aiplatform.googleapis.com` — no region prefix. Only *regional* endpoints prefix the host (`us-east5-aiplatform.googleapis.com`, `europe-west1-aiplatform.googleapis.com`). "global" is not a region name to prepend; it appears only in the URL **path**:

```
https://aiplatform.googleapis.com/v1/projects/{PROJECT}/locations/global/publishers/anthropic/models/{MODEL}:rawPredict
```

Confirmed HTTP 200 against `projects/klara-nonprod/locations/global/.../models/claude-sonnet-5:rawPredict`.

**The trap:** mechanically applying the regional template produces `global-aiplatform.googleapis.com`, which does not exist. Google's frontend answers it with a generic **HTML 404 page** — easy to misread as a routing/typo error, and distinct from the JSON 404 the real aiplatform API returns for a real-but-inaccessible resource (see [[Vertex AI 404 vs 403 distinguishes Model Garden enablement from IAM permission]]). Any loop that iterates regions by string-templating the host must special-case `global` to drop the prefix.

## Related

- [[Vertex AI 404 vs 403 distinguishes Model Garden enablement from IAM permission]]
- [[Claude Sonnet 5 confirmed working on Vertex AI for klara-nonprod]]
- [[Claude models are available on GCP Vertex AI Model Garden]]
- [[Virtual avatar presenter project design plan]]

%% ai-graph-start %%

**Related notes:**
- [[Claude Sonnet 5 confirmed working on Vertex AI for klara-nonprod]]
- [[Vertex models.list() shows the catalog, not what the project can invoke; Gemini 3 needs the global endpoint]]
- [[List Anthropic models on Vertex via the publisherModels REST endpoint]]
- [[Claude models are available on GCP Vertex AI Model Garden]]
- [[Vertex AI Model Garden enablement and quota are separate, per-model steps]]

%% ai-graph-end %%