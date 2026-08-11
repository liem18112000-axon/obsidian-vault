---
ai_hash: 218ba3139b62d56e
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-10
entities: []
source: deep-research pass, virtual-avatar project, 2026-07-10
status: evergreen
tags:
- claude
- anthropic
- gcp
- vertex-ai
- pricing
title: Claude models are available on GCP Vertex AI Model Garden
type: term
---

# Claude models are available on GCP Vertex AI Model Garden

Claude models (Opus 4.8 / Sonnet 5 / Fable 5 as of 2026) are listed on Google Cloud's Vertex AI Model Garden and can be called there, billed per-token through your existing GCP billing account at price parity with Anthropic's direct Console API. The global endpoint is the default; regional (non-global) Vertex endpoints carry roughly a 10% price premium, so prefer global unless data residency requires pinning to a region.

This matters whenever a project's cloud platform is GCP-only but the LLM needed is Claude: Vertex AI Model Garden is the GCP-native, officially sanctioned route (covered by Anthropic's Commercial Terms of Service, same as Console or Bedrock) — no separate Anthropic account or billing relationship required. It also resolves the tension where someone only has GCP for infra but still wants Claude specifically: everything (IAM, secrets, billing, networking) stays inside one GCP project.

Always spot-check the live Vertex rate card for the exact model before finalizing a cost estimate — pricing summaries from web search can lag the actual current SKU pricing.

## Related

- [[Claude subscription OAuth cannot power a third-party audience-facing app]]
- [[Claude Code runs on Vertex AI via three env vars with gcloud ADC]]
- [[Virtual avatar presenter project design plan]]

%% ai-graph-start %%

**Related notes:**
- [[Claude Code runs on Vertex AI via three env vars with gcloud ADC]]
- [[List Anthropic models on Vertex via the publisherModels REST endpoint]]
- [[Claude Sonnet 5 confirmed working on Vertex AI for klara-nonprod]]
- [[Claude subscription OAuth cannot power a third-party audience-facing app]]
- [[Vertex AI global endpoint host has no region prefix]]

%% ai-graph-end %%