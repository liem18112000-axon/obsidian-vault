---
title: "Raising negative-cache TTL turns transient failures into long-lived poison"
created: 2026-07-23
type: lesson
status: seedling
source: "session 2026-07-23 MaterializeGate stampede panel"
tags: [cache, ttl, gotcha, distributed-systems]
---

# Raising negative-cache TTL turns transient failures into long-lived poison

Raising the TTL of a cached **negative/absent result** (e.g. "migration incomplete = 0", cached 60s -> 3600s to cut recompute load) silently converts *transient failure states* into long-lived cluster-wide poison. Two paths bite:

1. **Dual-failure result**: if the recompute caches its default (false/0) even when *every* underlying check threw (empty result stream), then a 30s dependency blip (rolling deploy) that overlaps one recompute pins the wrong value for the full raised TTL — on every pod that refills its L1 from the shared L2. What used to self-heal in 60s now lasts an hour.
2. **Imminent-transition state**: a value computed from a state about to flip (status=IN_PROGRESS -> COMPLETED seconds later) gets the long TTL and outlives the transition; combined with the delete-then-stale-put race the completion event cannot fix it.

Rule: **condition the TTL on result provenance**, not just the result value — long TTL only when a check genuinely produced the answer (`hit.isPresent()`), short TTL (or no cache write) when the answer came from failure fallback, and short TTL when the source state signals an imminent transition (IN_PROGRESS). Each guard is ~1 line; omitting them falsifies any "never slower than today" claim.

## Related

- [[Delete-then-stale-put race bounds cache invalidation freshness at full TTL]]
- [[Per-pod single-flight kills cache stampede without semantic change]]
