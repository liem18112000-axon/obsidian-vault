---
ai_hash: 23144135a72d1f25
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-09
entities: []
source: luz_docs foldercount HLL badge implementation plan, 2026-07-09 — grounded
  in GroupService.alterGroup precedent
status: seedling
tags:
- concurrency
- mongodb
- cas
- optimistic-locking
- architecture
title: Shared aggregate write targets need CAS, not plain $set
type: lesson
---

# Shared aggregate write targets need CAS, not plain $set

When multiple concurrent writers (e.g. separate requests, separate pods) can target the SAME shared document to update a derived aggregate (a counter, a sketch, any "read current value, modify, write back" field), a plain unconditional $set is a read-modify-write race: two concurrent writers can both read the old value, and the second write silently clobbers the first, permanently losing an update.

This is a materially different risk than stamping a field on a document that only ONE writer ever touches (e.g. a field on the document you just created) -- that case is race-free by construction and a plain $set is fine there.

The fix is optimistic concurrency (CAS): add a version/counter field alongside the aggregate, read {value, version}, compute the new value, then write conditioned on the filter still matching the version you read ({_id, version: expectedVersion}), incrementing the version in the same update. On no-match (version moved under you), retry (bounded, e.g. via a retry-on-specific-exception annotation) rather than assuming success.

Concrete example I found already implemented in luz_docs: GroupService.alterGroup / addDocumentIdsToGroup / removeDocumentIdsFromGroup use exactly this shape via a MongoDB findAndModify with a version field in the filter, wrapped in @Retry(delay=100, retryOn=GroupVersionConflictException.class). That is the pattern to copy whenever a new feature needs to mutate a shared aggregate document from multiple concurrent writers -- do not reach for a plain $set just because it looks simpler.

See [[Per-document backfill executors assume no shared write target]] for the related backfill-side version of this same class of bug.

## Related

- [[Per-document backfill executors assume no shared write target]]

%% ai-graph-start %%

**Related notes:**
- [[Per-document backfill executors assume no shared write target]]
- [[Mongo unique-index insert as CAS when the cache has no putIfAbsent]]
- [[luz_docs bulk updateMany recompute is set-based - one event, batched literal-table pipeline, not per-doc fan-out]]
- [[luz_docs estimated-count POC drops CAS and backfill gate]]
- [[Whole-aggregate read-modify-write for a per-child toggle causes lost updates under concurrent sibling writes]]

%% ai-graph-end %%