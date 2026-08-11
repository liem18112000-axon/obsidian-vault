---
ai_hash: a888db9f243f2541
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-11
entities: []
source: session 2026-07-11, klara-nonprod Claude on Vertex setup
status: seedling
tags:
- gcp
- iam
- vertex-ai
- model-garden
title: consumerprocurement.entitlementManager grants Marketplace entitlement acceptance
type: term
---

# consumerprocurement.entitlementManager grants Marketplace entitlement acceptance

`roles/consumerprocurement.entitlementManager` is the GCP IAM role that lets a principal accept and manage a Marketplace-style procurement entitlement on a project — which is exactly what's needed to click "Enable" on a partner model card (e.g. Anthropic Claude) in Vertex AI **Model Garden**.

Without this role (or an equivalent broader role like Owner), the account can have full `roles/aiplatform.user` and still be unable to complete the one-time per-model enablement step that makes a partner model's publisher-model resource exist for the project. This is a separate permission axis from being able to *call* an already-enabled model — see [[Vertex AI 404 vs 403 distinguishes Model Garden enablement from IAM permission]].

## Related

- [[3 Resources/Cloud/GCP/Vertex AI/Vertex AI Model Garden enablement and quota are separate, per-model steps]]

%% ai-graph-start %%

**Related notes:**
- [[Vertex AI 404 vs 403 distinguishes Model Garden enablement from IAM permission]]
- [[Vertex AI Model Garden enablement and quota are separate, per-model steps]]
- [[List Anthropic models on Vertex via the publisherModels REST endpoint]]
- [[Claude models are available on GCP Vertex AI Model Garden]]

%% ai-graph-end %%