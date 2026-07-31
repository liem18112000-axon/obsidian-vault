---
title: "Claude models are available on GCP Vertex AI Model Garden"
created: 2026-07-10
type: term
status: evergreen
source: "deep-research pass, virtual-avatar project, 2026-07-10"
tags: [claude, anthropic, gcp, vertex-ai, pricing]
---

# Claude models are available on GCP Vertex AI Model Garden

Claude models (Opus 4.8 / Sonnet 5 / Fable 5 as of 2026) are listed on Google Cloud's Vertex AI Model Garden and can be called there, billed per-token through your existing GCP billing account at price parity with Anthropic's direct Console API. The global endpoint is the default; regional (non-global) Vertex endpoints carry roughly a 10% price premium, so prefer global unless data residency requires pinning to a region.

This matters whenever a project's cloud platform is GCP-only but the LLM needed is Claude: Vertex AI Model Garden is the GCP-native, officially sanctioned route (covered by Anthropic's Commercial Terms of Service, same as Console or Bedrock) — no separate Anthropic account or billing relationship required. It also resolves the tension where someone only has GCP for infra but still wants Claude specifically: everything (IAM, secrets, billing, networking) stays inside one GCP project.

Always spot-check the live Vertex rate card for the exact model before finalizing a cost estimate — pricing summaries from web search can lag the actual current SKU pricing.

## Related
- [[Claude subscription OAuth cannot power a third-party audience-facing app]]
- [[Virtual avatar presenter project design plan]]

## Related

- [[Claude subscription OAuth cannot power a third-party audience-facing app]]
- [[Virtual avatar presenter project design plan]]
