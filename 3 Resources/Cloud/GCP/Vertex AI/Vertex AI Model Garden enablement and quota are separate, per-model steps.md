---
ai_hash: 1fb4b6a4c6b9ebcf
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-11
entities: []
source: session 2026-07-11, klara-nonprod Claude on Vertex setup
status: seedling
tags:
- gcp
- vertex-ai
- quota
- model-garden
- gotcha
title: Vertex AI Model Garden enablement and quota are separate, per-model steps
type: lesson
---

# Vertex AI Model Garden enablement and quota are separate, per-model steps

Enabling a partner model (e.g. a specific Claude model) in Vertex AI Model Garden and having usable **quota** for it are two separate steps, and enablement is scoped **per model**, not to "all models from that publisher."

Evidence from a real setup: enabling `claude-sonnet-5` via Model Garden made that exact model ID stop 404ing (it now resolves and accepts requests) — but `claude-haiku-4-5` on the same project stayed `404 NOT_FOUND`, confirming enablement doesn't cascade to other models from the same publisher.

Separately, immediately after enabling `claude-sonnet-5`, the very first `rawPredict` call (not a repeat/burst) returned `429 RESOURCE_EXHAUSTED` for `online_prediction_input_tokens_per_minute_per_base_model`. A 429 on the *first ever* call to a freshly-enabled model means the project's default quota for that base model is 0, not that real traffic exhausted it — a quota increase must be requested separately via Cloud Console → IAM & Admin → Quotas. Model Garden "Enable" only grants access to the resource; it doesn't provision throughput.

## Related

- [[consumerprocurement.entitlementManager grants Marketplace entitlement acceptance]]
- [[Claude Sonnet 5 confirmed working on Vertex AI for klara-nonprod]]

%% ai-graph-start %%

**Related notes:**
- [[Claude Sonnet 5 confirmed working on Vertex AI for klara-nonprod]]
- [[Vertex AI 404 vs 403 distinguishes Model Garden enablement from IAM permission]]
- [[Claude models are available on GCP Vertex AI Model Garden]]
- [[Vertex AI global endpoint host has no region prefix]]
- [[List Anthropic models on Vertex via the publisherModels REST endpoint]]

%% ai-graph-end %%