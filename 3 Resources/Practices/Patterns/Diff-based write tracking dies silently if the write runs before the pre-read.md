---
ai_hash: f7979ed5a6f70b45
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-05
entities: []
source: TrackingJsonStoreClient tracked() review, session 2026-06-05
status: budding
tags:
- change-tracking
- ordering
- gotcha
- luz-docs
title: Diff-based write tracking dies silently if the write runs before the pre-read
type: lesson
---

# Diff-based write tracking dies silently if the write runs before the pre-read

In a write-tracking wrapper that diffs before/after state, the **pre-read must execute before the write** — the correct order is gate -> pre-read -> write -> async diff+fire. If the write is hoisted above the pre-read (tempting when refactoring so the gate/bypass path shares one `write.get()` call), tracking silently dies rather than erroring:

- update/replace: the "before" snapshot reads the already-written document -> diff(post, post) is empty -> the no-diff suppression swallows every event;
- delete: the pre-read finds nothing -> diff(null, null) -> no delete events.

Nothing fails loudly — events just stop. Worth a unit test asserting an event fires for a value-changing update, since no compiler or runtime check catches the ordering.

Live instance: luz_docs `TrackingJsonStoreClient.tracked()` as committed in 701cebb9c has write-before-pre-read (flagged, owner chose to commit; fix = move `var response = write.get()` below the pre-read and keep the gate returning `write.get()` directly).

## Related

- [[luz_docs tracked-write template folds the four single-doc ops into one gate-preread-write-fire method]]

%% ai-graph-start %%

**Related notes:**
- [[luz_docs change tracking covers updateMany-deleteMany via projected before-after snapshots keyed by id]]
- [[luz_docs tracked-write template folds the four single-doc ops into one gate-preread-write-fire method]]
- [[luz_docs DocumentChangeObserver base owns the reload-recompute-restamp template]]
- [[luz_docs change tracking dropped the ChangeOrigin event marker - thread-local suppression is the loop guard]]
- [[luz_docs JsonStore change tracking via client-layer wrapper and CDI async events]]

%% ai-graph-end %%