---
ai_hash: 891d6f507991ec9e
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-22
entities: []
source: MaterializeCampaignCheckService 2026-07-22
status: seedling
tags:
- java
- cdi
- concurrency
- pattern
title: Dedup CDI fireAsync per key with in-flight set released in whenComplete
type: howto
---

# Dedup CDI fireAsync per key with in-flight set released in whenComplete

To dedup CDI async event storms per key: keep a `ConcurrentHashMap.newKeySet()` of in-flight keys; `if (!set.add(key)) return;` before `fireAsync`, and release via the returned CompletionStage — `fireAsync(...).whenComplete((e,t) -> set.remove(key))` — which fires whether the @ObservesAsync observer succeeds or throws (observer exceptions complete the stage exceptionally, they do not vanish). Synchronous fire failure needs its own `remove` in the catch. Used in MaterializeCampaignCheckService.onCampaignCheckRequested to collapse per-request campaign checks to one per tenant at a time; TTL caching downstream handles the "already checked recently" case, the set handles "currently being checked".

%% ai-graph-start %%

**Related notes:**
- [[Per-pod single-flight kills cache stampede without semantic change]]
- [[Extract shared root of near-identical CDI beans into a static common helper + Spec + supplier]]
- [[Async CDI observers must receive the session token via the event payload]]
- [[Cascade-marker pattern for crash-safe async retry]]
- [[Campaign COMPLETED status is only trusted after re-verifying document state (truth-check gate)]]

%% ai-graph-end %%