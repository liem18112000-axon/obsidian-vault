---
title: "LEO Personalization Engine uses config-first Gorse plus a Python re-rank layer"
created: 2026-07-22
type: argument
status: budding
source: "Deep research 2026-07-22; full report: leocdp-personalization-engine/docs/Research - Customizing Gorse Models & Behavior for LEO (EN).md"
tags: [leo-cdp, gorse, decision, architecture]
---

# LEO Personalization Engine uses config-first Gorse plus a Python re-rank layer

**Decision (2026-07-22):** LEO customizes Gorse **config-first** — feedback value expressions, `mf` CF + `fm` ranker with an explicit `recommenders` merge list, one named recommender block per shelf (Chosen For You = CF+FM pipeline, Trending = Expr leaderboard, New Arrivals = latest / timestamp filter, Best Offers = external QuickJS recommender), `[recommend.fallback]` for cold users — plus a **thin Python re-rank layer** in the `gorse.py` adapter for everything Gorse cannot do per request: stock/margin/channel rules, cross-shelf dedupe, per-request exclusions (open upstream gap, gorse-io/gorse#1345), and LEO's gating thresholds.

**Why not fork:** Gorse serves precomputed cached recs with no per-request scoring hook, has no model plugin API (closed enums `bpr`/`als`, `none|fm|llm`), and is pre-1.0 with breaking minor versions — a fork buys little and carries a permanent maintenance tax. The ports-and-adapters design absorbs the re-rank step inside the adapter; the `Recommender` port is unchanged.

Open question that affects the login webhook: what triggers per-user cache regeneration in v0.5 (is `cache_expire = 120h` the only freshness knob)? Needs an empirical test.

## Related

- [[Gorse precomputes recommendations offline — per-request scoring is impossible inside it]]
- [[Gorse v0.5 declares custom recommenders as named config blocks with Expr expressions]]
- [[Gorse v0.4 docs describe a defunct config schema — trust the shipped config.toml template]]
