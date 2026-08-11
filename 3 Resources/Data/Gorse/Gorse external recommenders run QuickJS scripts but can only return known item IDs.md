---
ai_hash: 82c5e29db55a31db
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-22
entities: []
source: Deep research 2026-07-22 — gorse.io external-recommender docs + gitrec
status: budding
tags:
- gorse
- quickjs
- gotcha
- config
title: Gorse external recommenders run QuickJS scripts but can only return known item
  IDs
type: howto
---

# Gorse external recommenders run QuickJS scripts but can only return known item IDs

`[[recommend.external]]` (Gorse v0.5+, refined in v0.5.3) embeds a JavaScript script executed by an in-process **QuickJS** engine inside the retrieval pipeline. The script gets synchronous `fetch()` (GET/POST + env-var access) and must return an array of item IDs; those IDs join the candidate pool and are ranked by the FM/LLM ranker like any other source. This is the sanctioned way to inject business lists (offers, editorial picks, external trending) without forking — gitrec injects GitHub Trending this way.

**Gotcha:** "Item IDs that do not exist in the recommender system will be ignored" — business-rule items must already be ingested into Gorse's item catalog before the script can recommend them.

## Related

- [[Gorse v0.5 declares custom recommenders as named config blocks with Expr expressions]]

%% ai-graph-start %%

**Related notes:**
- [[Gorse v0.5 declares custom recommenders as named config blocks with Expr expressions]]
- [[Gorse precomputes recommendations offline — per-request scoring is impossible inside it]]
- [[Gorse LLM reranker is a rerank-API integration, not chat completions]]
- [[LEO Personalization Engine uses config-first Gorse plus a Python re-rank layer]]
- [[Gorse v0.4 docs describe a defunct config schema — trust the shipped config.toml template]]

%% ai-graph-end %%