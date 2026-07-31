---
ai_hash: f703616c6c5ed7e9
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-05
entities: []
source: DocBulkMaterializeObserver implementation, session 2026-06-05
status: budding
tags:
- luz-docs
- materialize
- change-tracking
- bulk-ops
- performance
- design-decision
title: luz_docs bulk updateMany recompute is set-based - one event, batched literal-table
  pipeline, not per-doc fan-out
type: model
---

# luz_docs bulk updateMany recompute is set-based - one event, batched literal-table pipeline, not per-doc fan-out

A tracked `updateMany` touching materialize trigger fields must NOT fan out N per-doc recompute events (~3N round trips: reload + recompute + re-stamp each). luz_docs instead fires **one** `BulkDocumentChangeEvent` carrying the pre-matched ids (no per-doc diff — bulk reactions recompute from current truth, so a diff would be unused), and `DocBulkMaterializeObserver` recomputes **set-based** per batch of `BULK_RECOMPUTE_BATCH` (200) ids:

1. one projected read of the batch docs → distinct folderIds,
2. one folder prefetch (`loadFoldersById`),
3. one `updateMany {_id: {$in: batch}}` with a literal-table pipeline that rebuilds ALL FOUR sentinels — including `_folderNames` via an id→name table, which the parent-change cascade deliberately skips but a bulk `folderIds` change invalidates.

Constant round trips per batch regardless of N; batching bounds both the `$in` and the inlined tables far below the 16 MB command cap.

Correctness props: `$range` input is `$ifNull`-guarded so docs without `folderIds` degrade to empty arrays (the by-id filter, unlike `{folderIds: {$in: ...}}`, does not guarantee the field exists — raw `$size` would WriteError); a 207 from `updateManyByFilter` (matched != modified) is swallowed as benign — Mongo does not count an update that produces an identical doc as modified, so already-correct sentinels are expected, not a partial failure; the find→updateMany window is the same accepted race as single-op tracking; idempotent recompute + @Retry converge.

## Related

- [[luz_docs change tracking covers updateMany-deleteMany via projected before-after snapshots keyed by id]]
- [[luz_docs DocumentChangeObserver base owns the reload-recompute-restamp template]]
- [[3 Resources/Data/MongoDB/MongoDB forbids $lookup inside update pipeline (WriteError 72)]]

> [!warning] Status 2026-06-05: never merged — removed from the working tree the same day (user decision, "remove modify-many code for now"). The design lives in this note + the session handoff report only.

%% ai-graph-start %%

**Related notes:**
- [[luz_docs change tracking covers updateMany-deleteMany via projected before-after snapshots keyed by id]]
- [[luz-docs updateManyByFilter requires every targeted document to actually change]]
- [[Materialize bulk PATCH fans out into N serial per-doc PATCH calls]]
- [[luz_docs parent-change cascade pipeline rebuilds _folderSecurityClassCodes positionally then re-derives the sentinels]]
- [[MongoDB forbids $lookup inside update pipeline (WriteError 72)]]

%% ai-graph-end %%