---
ai_hash: 4dfdecbd42db8251
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-08
entities: []
source: 'luz_docs materialize review #11, 2026-06-08'
status: seedling
tags:
- caching
- performance
- luz-docs
- materialize
- gotcha
title: A negative cache must be a distinct state from a cache miss, or its TTL is
  a dead write
type: lesson
---

# A negative cache must be a distinct state from a cache miss, or its TTL is a dead write

A cache lookup has **three** outcomes, not two: positive hit, **negative hit** (a stored "the answer is no/false"), and **miss** (nothing cached / expired). If the read path collapses negative-hit into miss, the expensive fallback runs on every request and the negative entry's TTL becomes a **dead write** — written but never read to short-circuit. The negative TTL only damps load if the code can tell "cached false" apart from "absent".

## Symptom
A short negative TTL that "has no effect" — load stays high during exactly the window the negative cache was meant to protect.

## Fix
Make the cache read return a tri-state. Map the stored negative value to its own state and short-circuit the fallback on it; only a true miss falls through to recompute.

## Concrete instance — luz_docs materialize gate (#11)
`MaterializeGate.readCompletionStatusFromCache` mapped both stored `"false"` and a miss to one `INCOMPLETE` state, and `isMaterializationComplete` only short-circuited on `COMPLETE` — so every gated request during backfill paid a full unmaterialized-`countByFilter` over the documents collection, and `TTL_MATERIALISATION_INCOMPLETE_SECONDS=60` was inert. Fix: enum is now tri-state `COMPLETE / INCOMPLETE / UNKNOWN`, with a `static MaterializeStatus.resolve(String)` switch mapping cache value → state (`"true"`→COMPLETE, `"false"`→INCOMPLETE, null/anything else→UNKNOWN). The gate returns `true` on COMPLETE, **`false` on INCOMPLETE** (negative-cache hit, no repo call), and only **UNKNOWN** (true miss or cache error) falls through to `countByFilter`. Naming choice: `UNKNOWN` = "cache said nothing", distinct from `INCOMPLETE` = "cache said not-done".

## Related

- [[materialize-code-review]]
- [[Deterministic Mongo pipeline updates return matched-not-modified; treat jsonstore SC_MULTI_STATUS as benign]]

%% ai-graph-start %%

**Related notes:**
- [[DualCache L1 write ignores per-call TTL (uses domain default)]]
- [[Raising negative-cache TTL turns transient failures into long-lived poison]]
- [[Deterministic Mongo pipeline updates return matched-not-modified; treat jsonstore SC_MULTI_STATUS as benign]]
- [[Cache-epoch invalidation fails if the epoch is read through a local L1]]
- [[luz_docs stamps _shard on create to keep sharding gate stable]]

%% ai-graph-end %%