---
ai_hash: d2feb81449c07832
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-11
entities: []
source: session 2026-07-11, klara-nonprod Claude on Vertex setup
status: seedling
tags:
- gcp
- vertex-ai
- iam
- troubleshooting
title: Vertex AI 404 vs 403 distinguishes Model Garden enablement from IAM permission
type: lesson
---

# Vertex AI 404 vs 403 distinguishes Model Garden enablement from IAM permission

On Vertex AI, calling a publisher model endpoint (GET or `:rawPredict`) that returns `404 NOT_FOUND` with the message "was not found or your project does not have access to it" — while the caller already holds `roles/aiplatform.user` on the project — means the model has not been **enabled in Model Garden** for that project, not that the model ID or region is wrong.

This is distinguishable from a plain IAM problem: when the same call was made from an account that lacked `roles/aiplatform.user` entirely on a different project, Vertex returned `403 PERMISSION_DENIED` ("Permission 'aiplatform.endpoints.predict' denied ... or it may not exist") instead. So the diagnostic rule is:
- **403 permission-denied** → missing IAM role on the project.
- **404 "not found or your project does not have access"** (with the base IAM role already present) → the specific partner model needs Model Garden enablement (see [[Vertex AI Model Garden enablement and quota are separate, per-model steps]]).

Confirmed by cross-testing the identical model+region combo across two GCP projects with different IAM states, and by getting a real `200` from a known-good model (`gemini-2.5-flash`) on the same project/region to rule out a broader auth/API-access problem before concluding it was a partner-model-specific enablement gap.

## Related

- [[consumerprocurement.entitlementManager grants Marketplace entitlement acceptance]]
- [[Vertex AI Model Garden enablement and quota are separate, per-model steps]]

%% ai-graph-start %%

**Related notes:**
- [[consumerprocurement.entitlementManager grants Marketplace entitlement acceptance]]
- [[Vertex models.list() shows the catalog, not what the project can invoke; Gemini 3 needs the global endpoint]]
- [[Vertex AI Model Garden enablement and quota are separate, per-model steps]]
- [[Vertex AI global endpoint host has no region prefix]]
- [[GCP APIs must be enabled individually per project]]

%% ai-graph-end %%