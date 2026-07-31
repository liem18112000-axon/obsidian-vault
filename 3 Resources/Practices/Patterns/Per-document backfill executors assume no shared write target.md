---
title: "Per-document backfill executors assume no shared write target"
created: 2026-07-09
type: lesson
status: seedling
source: "luz_docs foldercount HLL badge implementation plan, 2026-07-09"
tags: [concurrency, batch-processing, migration, architecture]
---

# Per-document backfill executors assume no shared write target

A backfill/migration executor shaped as "for each document, in parallel, compute and write that document's OWN field" (the common pattern for stamping a derived field onto every row) is race-free ONLY because no two documents ever share a write target -- each parallel worker owns a disjoint piece of state.

That shape breaks silently if you reuse it for a DIFFERENT kind of backfill: "for each document, contribute to a SHARED aggregate owned by some other entity" (e.g. many documents in a folder all need to update that one folder's counter/sketch). Copy-pasting the per-document-parallel-own-field executor onto this problem reproduces a lost-update race at backfill scale, because many workers now write-modify-write the same few shared targets concurrently.

The correct shape for a many-to-few aggregation backfill is scan-then-flush: accumulate all the per-item contributions into an in-memory map keyed by the shared target (synchronizing only the in-memory accumulation step, not each individual document's write), then do exactly ONE write per shared target at the end (a flush phase). This confines the concurrency-sensitive part to memory (cheap to get right) and makes the actual persistence a single, safe, non-racing write per target.

I found this distinction while planning a HyperLogLog per-folder count-sketch backfill in luz_docs: the existing MaterializeMigrationExecutor and ParallelizeMigrationExecutor are both the "own field" shape and are NOT valid templates for the new "shared folder aggregate" backfill, even though they look superficially identical (same pagination style, same MigrationExecutor interface).

See [[Shared aggregate write targets need CAS, not plain $set]] for the live-write-path version of the same underlying problem.

## Related

- [[Shared aggregate write targets need CAS]]
- [[not plain $set]]
