---
title: "Whole-aggregate read-modify-write for a per-child toggle causes lost updates under concurrent sibling writes"
created: 2026-07-05
type: gotcha
status: seedling
source: "vinnstack code review 2026-07-05, approveStoryFlow"
tags: [concurrency, lost-update, persistence, vinnstack]
---

# Whole-aggregate read-modify-write for a per-child toggle causes lost updates under concurrent sibling writes

When a mutation flips ONE field on ONE child of an aggregate but implements it as load-whole-aggregate → mutate-in-memory → save-whole-aggregate (delete-and-reinsert all children), two concurrent mutations on DIFFERENT children of the SAME aggregate silently lose one update. Both requests load the same snapshot (child A=draft, B=draft); request A saves {A=approved}; request B, holding the stale snapshot, reinserts everything and rewrites A back to draft. A's change is gone.

A DB transaction around the save does NOT help — it prevents a half-written record, not a lost update across two sequential read-modify-write cycles. You need row-level locking (SELECT … FOR UPDATE), an optimistic version column checked on write, or — best — a TARGETED update (UPDATE child SET field=… WHERE id=…) that never rehydrates the siblings at all.

Tell: a "fast" no-LLM/instant action built on a load→mutate→save aggregate helper that was originally written for slow, naturally-serialized operations (e.g. an LLM generation). The speed is what makes the overlap reachable. The client often guards re-clicks of the SAME item but not concurrent actions on different items of the same parent.

## Related

- [[Secondary-write failures should fail loud when their silent version was the actual bug]]
