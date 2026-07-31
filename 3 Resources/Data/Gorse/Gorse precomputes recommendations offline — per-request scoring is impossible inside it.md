---
title: "Gorse precomputes recommendations offline — per-request scoring is impossible inside it"
created: 2026-07-22
type: concept
status: budding
source: "Deep research 2026-07-22 — gorse.io docs + gorse-io/gorse v0.5.11 source"
tags: [gorse, recommender-systems, architecture]
---

# Gorse precomputes recommendations offline — per-request scoring is impossible inside it

Gorse (v0.5.x) generates recommendations in an offline retrieval → rank pipeline on worker nodes and stores the results in a cache database. The REST serving path (`GET /api/recommend/{user}`) only applies request-time filtering to that cache: removing already-read items, category filters, pagination, and fallback recommenders. There is no hook to run custom scoring per request — the session-based endpoint is the sole per-request computation, and it merely aggregates *cached* item-to-item similarity scores.

**Consequence:** any per-request business logic (stock, margin, cross-shelf dedupe, exclusion lists) has to re-rank Gorse's API output in your own service layer; this is a design property, not a config gap.

Code evidence: `worker/worker.go` (ticker loop calling `Recommend()`), `server/rest.go` (`CacheClient.SearchScores`, no scoring hook).

## Related

- [[Gorse v0.5 declares custom recommenders as named config blocks with Expr expressions]]
- [[LEO Personalization Engine uses config-first Gorse plus a Python re-rank layer]]
