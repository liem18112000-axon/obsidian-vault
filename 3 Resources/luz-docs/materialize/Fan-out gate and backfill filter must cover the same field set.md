---
title: "Fan-out gate and backfill filter must cover the same field set"
created: 2026-06-19
type: lesson
status: seedling
source: "session 2026-06-19 LUZ-154613"
tags: [luz-docs, materialize, design, gotcha]
---

# Fan-out gate and backfill filter must cover the same field set

When one filter decides *"safe to enable fan-out / parallel read path"* and another decides *"what still needs backfilling"*, they must cover the **same field set** — otherwise a doc that satisfies the gate but not the backfill (or vice-versa) produces a silent undercount or skipped work. Drift between two such filters is the bug class, not any single missing field.

**Concrete case (luz-docs materialize):** `buildUnmaterializedFilter` (the gate) had drifted from `buildBackfillFilter` (the migration) — backfill required `_shard`, the gate did not. Result: docs with the 3 read fields but no `_shard` passed the gate and the parallel shard-count dropped them.

**Defense:** make one filter the single source of truth and have the other delegate (`buildBackfillFilter` now returns `buildUnmaterializedFilter()`) so they can never drift again.

Related: [[Materialize gate must require _shard or parallelized count undercounts]]

## Related

- [[Materialize gate must require _shard or parallelized count undercounts]]
- [[luz_docs_statistic computes per-tenant unmaterializedDocuments count]]
