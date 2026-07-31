---
title: "luz_docs change tracking covers updateMany-deleteMany via projected before-after snapshots keyed by id"
created: 2026-06-05
type: model
status: budding
source: "TrackingJsonStoreClient many-ops, session 2026-06-05"
tags: [luz-docs, change-tracking, mongodb, bulk-ops, design-decision]
---

# luz_docs change tracking covers updateMany-deleteMany via projected before-after snapshots keyed by id

luz_docs `TrackingJsonStoreClient` tracks bulk writes without per-doc reads: snapshot the matching docs with one `find` projected to `_id` + the collection's tracked fields only (cheap even for big match sets), run the bulk op, then per-doc:

- **updateMany** — re-read *only the pre-matched ids* (`{_id: {$in: [...]}}`, same projection) and fire one `UPDATE_MANY` event per doc whose tracked-field diff is non-empty. Docs that started matching between the two reads are an accepted race, same as the single-op pre-read→write window.
- **deleteMany** — no post-read; fire `DELETE_MANY` with `diff(before, null)` per snapshotted doc.
- **insertMany** stays pass-through: it has zero callers in luz_docs and the jsonstore body/response shape is not visible from the repo — tracking it would be speculation (decided 2026-06-05 with Liem).

Three load-bearing details:
1. The cheap gate parses the update for touched fields — including **aggregation-pipeline updates** (array of `$set`/`$addFields` stages, plus pipeline `$unset` string/array forms). This is what stops the materialize cascades from tracking themselves: their pipelines only touch untracked sentinel fields, so the gate short-circuits before any pre-read.
2. Snapshot failure degrades to *firing nothing* (logged warning), never to failing the write.
3. Per-doc events (not one bulk event) keep every observer single-doc — `DocumentChangeObserver` subclasses work unchanged; the enum just gained `UPDATE_MANY`/`DELETE_MANY` and an `isDelete()` fold used by skip-gates.

## Related

- [[luz_docs DocumentChangeObserver base owns the reload-recompute-restamp template]]
- [[luz_docs change tracking dropped the ChangeOrigin event marker - thread-local suppression is the loop guard]]

> [!warning] Status 2026-06-05: REMOVED from the codebase for now (user decision, same day it was built). Both layers taken out: the per-doc UPDATE_MANY/DELETE_MANY tracking and the bulk set-based recompute. updateMany/deleteMany are plain pass-throughs again; tracking covers single-doc ops only. This note preserves the working design for when it is reintroduced; the per-doc variant also exists in git history (commit 61e2777a2 on kepler/sprint-158/test-json-change-tracking, removed by the follow-up commit).
