---
ai_hash: 5d312dd4241e284d
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-22
entities:
- LEO Personalization Engine
- Gorse
- Python re-rank layer
- Config-first approach
- Feedback value expressions
- MF CF
- FM ranker
- Recommenders merge list
- Shelf
- Chosen For You
- CF+FM pipeline
- Trending
- Expr leaderboard
- New Arrivals
- Latest / timestamp filter
- Best Offers
- External QuickJS recommender
- Fallback mechanism
- Cold users
- gorse.py adapter
- Stock rules
- Margin rules
- Channel rules
- Cross-shelf dedupe
- Per-request exclusions
- 'Gorse issue #1345'
- LEO's gating thresholds
- Forking Gorse
- Precomputed cached recommendations
- Per-request scoring hook
- Model plugin API
- BPR
- ALS
- FM
- LLM
- Gorse pre-1.0
- Breaking minor versions
- Maintenance tax
- Ports-and-adapters design
- Recommender port
- Login webhook
- Per-user cache regeneration
- Gorse v0.5
- cache_expire = 120h
- Offline recommendation precomputation
- Custom recommenders (Gorse v0.5)
- Named config blocks
- Expr expressions
- Gorse v0.4 docs
- Defunct config schema
- Config.toml template
source: 'Deep research 2026-07-22; full report: leocdp-personalization-engine/docs/Research
  - Customizing Gorse Models & Behavior for LEO (EN).md'
status: budding
tags:
- leo-cdp
- gorse
- decision
- architecture
title: LEO Personalization Engine uses config-first Gorse plus a Python re-rank layer
type: argument
---

# LEO Personalization Engine uses config-first Gorse plus a Python re-rank layer

**Decision (2026-07-22):** LEO customizes Gorse **config-first** — feedback value expressions, `mf` CF + `fm` ranker with an explicit `recommenders` merge list, one named recommender block per shelf (Chosen For You = CF+FM pipeline, Trending = Expr leaderboard, New Arrivals = latest / timestamp filter, Best Offers = external QuickJS recommender), `[recommend.fallback]` for cold users — plus a **thin Python re-rank layer** in the `gorse.py` adapter for everything Gorse cannot do per request: stock/margin/channel rules, cross-shelf dedupe, per-request exclusions (open upstream gap, gorse-io/gorse#1345), and LEO's gating thresholds.

**Why not fork:** Gorse serves precomputed cached recs with no per-request scoring hook, has no model plugin API (closed enums `bpr`/`als`, `none|fm|llm`), and is pre-1.0 with breaking minor versions — a fork buys little and carries a permanent maintenance tax. The ports-and-adapters design absorbs the re-rank step inside the adapter; the `Recommender` port is unchanged.

Open question that affects the login webhook: what triggers per-user cache regeneration in v0.5 (is `cache_expire = 120h` the only freshness knob)? Needs an empirical test.

## Related

- [[Gorse precomputes recommendations offline — per-request scoring is impossible inside it]]
- [[Gorse v0.5 declares custom recommenders as named config blocks with Expr expressions]]
- [[Gorse v0.4 docs describe a defunct config schema — trust the shipped config.toml template]]

%% ai-graph-start %%

**Related notes:**
- [[Gorse precomputes recommendations offline — per-request scoring is impossible inside it]]
- [[Gorse v0.5 declares custom recommenders as named config blocks with Expr expressions]]
- [[Gorse config exposes model family and cadence, never hyperparameters]]
- [[Gorse LLM reranker is a rerank-API integration, not chat completions]]
- [[Gorse v0.4 docs describe a defunct config schema — trust the shipped config.toml template]]

**Relations:**
- LEO Personalization Engine — *uses* — Gorse
- LEO Personalization Engine — *uses* — Python re-rank layer
- Gorse — *is configured by* — Config-first approach
- Config-first approach — *includes* — Feedback value expressions
- Config-first approach — *uses* — MF CF
- Config-first approach — *uses* — FM ranker
- Config-first approach — *defines* — Recommenders merge list
- Config-first approach — *supports* — Shelf
- Chosen For You — *is a type of* — Shelf
- Chosen For You — *uses* — CF+FM pipeline
- Trending — *is a type of* — Shelf
- Trending — *uses* — Expr leaderboard
- New Arrivals — *is a type of* — Shelf
- New Arrivals — *uses* — Latest / timestamp filter
- Best Offers — *is a type of* — Shelf
- Best Offers — *uses* — External QuickJS recommender
- Config-first approach — *includes* — Fallback mechanism
- Fallback mechanism — *targets* — Cold users
- Python re-rank layer — *is implemented in* — gorse.py adapter
- Python re-rank layer — *handles* — Stock rules
- Python re-rank layer — *handles* — Margin rules
- Python re-rank layer — *handles* — Channel rules
- Python re-rank layer — *handles* — Cross-shelf dedupe
- Python re-rank layer — *handles* — Per-request exclusions
- Python re-rank layer — *handles* — LEO's gating thresholds
- Gorse — *has limitation* — Per-request exclusions
- Per-request exclusions — *is tracked in* — Gorse issue #1345
- Gorse — *provides* — Precomputed cached recommendations
- Gorse — *lacks* — Per-request scoring hook
- Gorse — *lacks* — Model plugin API
- Model plugin API — *supports* — BPR
- Model plugin API — *supports* — ALS
- Model plugin API — *supports* — FM
- Model plugin API — *supports* — LLM
- Gorse — *is* — Gorse pre-1.0
- Gorse pre-1.0 — *has* — Breaking minor versions
- Forking Gorse — *causes* — Maintenance tax
- Ports-and-adapters design — *accommodates* — Python re-rank layer
- gorse.py adapter — *follows* — Ports-and-adapters design
- Recommender port — *is unaffected by* — Ports-and-adapters design
- Login webhook — *is affected by* — Per-user cache regeneration
- Per-user cache regeneration — *is a feature of* — Gorse v0.5
- cache_expire = 120h — *controls* — Per-user cache regeneration
- Gorse — *performs* — Offline recommendation precomputation
- Gorse v0.5 — *supports* — Custom recommenders (Gorse v0.5)
- Custom recommenders (Gorse v0.5) — *are defined as* — Named config blocks
- Named config blocks — *use* — Expr expressions
- Gorse v0.4 docs — *describe* — Defunct config schema
- Config.toml template — *is authoritative over* — Gorse v0.4 docs

%% ai-graph-end %%