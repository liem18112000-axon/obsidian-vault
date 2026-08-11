---
ai_hash: 21c4d328790bf761
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-22
entities: []
source: Deep research 2026-07-22 — gorse.io docs + gorse-io/gorse v0.5.11 source
status: budding
tags:
- gorse
- recommender-systems
- architecture
title: Gorse precomputes recommendations offline — per-request scoring is impossible
  inside it
type: concept
---

# Gorse precomputes recommendations offline — per-request scoring is impossible inside it

Gorse (v0.5.x) generates recommendations in an offline retrieval → rank pipeline on worker nodes and stores the results in a cache database. The REST serving path (`GET /api/recommend/{user}`) only applies request-time filtering to that cache: removing already-read items, category filters, pagination, and fallback recommenders. There is no hook to run custom scoring per request — the session-based endpoint is the sole per-request computation, and it merely aggregates *cached* item-to-item similarity scores.

**Consequence:** any per-request business logic (stock, margin, cross-shelf dedupe, exclusion lists) has to re-rank Gorse's API output in your own service layer; this is a design property, not a config gap.

Code evidence: `worker/worker.go` (ticker loop calling `Recommend()`), `server/rest.go` (`CacheClient.SearchScores`, no scoring hook).

## Related

- [[Gorse v0.5 declares custom recommenders as named config blocks with Expr expressions]]
- [[LEO Personalization Engine uses config-first Gorse plus a Python re-rank layer]]

%% ai-graph-start %%

**Related notes:**
- [[LEO Personalization Engine uses config-first Gorse plus a Python re-rank layer]]
- [[Gorse v0.5 declares custom recommenders as named config blocks with Expr expressions]]
- [[Gorse LLM reranker is a rerank-API integration, not chat completions]]
- [[Gorse external recommenders run QuickJS scripts but can only return known item IDs]]
- [[Gorse config exposes model family and cadence, never hyperparameters]]

%% ai-graph-end %%