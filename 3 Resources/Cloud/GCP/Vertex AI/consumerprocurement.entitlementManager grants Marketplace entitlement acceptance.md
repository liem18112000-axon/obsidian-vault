---
title: "consumerprocurement.entitlementManager grants Marketplace entitlement acceptance"
created: 2026-07-11
type: term
status: seedling
source: "session 2026-07-11, klara-nonprod Claude on Vertex setup"
tags: [gcp, iam, vertex-ai, model-garden]
---

# consumerprocurement.entitlementManager grants Marketplace entitlement acceptance

`roles/consumerprocurement.entitlementManager` is the GCP IAM role that lets a principal accept and manage a Marketplace-style procurement entitlement on a project — which is exactly what's needed to click "Enable" on a partner model card (e.g. Anthropic Claude) in Vertex AI **Model Garden**.

Without this role (or an equivalent broader role like Owner), the account can have full `roles/aiplatform.user` and still be unable to complete the one-time per-model enablement step that makes a partner model's publisher-model resource exist for the project. This is a separate permission axis from being able to *call* an already-enabled model — see [[Vertex AI 404 vs 403 distinguishes Model Garden enablement from IAM permission]].

## Related

- [[Vertex AI Model Garden enablement and quota are separate]]
- [[per-model steps]]
