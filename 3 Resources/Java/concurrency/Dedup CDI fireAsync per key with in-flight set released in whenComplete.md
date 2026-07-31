---
title: "Dedup CDI fireAsync per key with in-flight set released in whenComplete"
created: 2026-07-22
type: howto
status: seedling
source: "MaterializeCampaignCheckService 2026-07-22"
tags: [java, cdi, concurrency, pattern]
---

# Dedup CDI fireAsync per key with in-flight set released in whenComplete

To dedup CDI async event storms per key: keep a `ConcurrentHashMap.newKeySet()` of in-flight keys; `if (!set.add(key)) return;` before `fireAsync`, and release via the returned CompletionStage — `fireAsync(...).whenComplete((e,t) -> set.remove(key))` — which fires whether the @ObservesAsync observer succeeds or throws (observer exceptions complete the stage exceptionally, they do not vanish). Synchronous fire failure needs its own `remove` in the catch. Used in MaterializeCampaignCheckService.onCampaignCheckRequested to collapse per-request campaign checks to one per tenant at a time; TTL caching downstream handles the "already checked recently" case, the set handles "currently being checked".
