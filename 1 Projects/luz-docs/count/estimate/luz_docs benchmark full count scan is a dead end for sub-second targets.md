---
title: "luz_docs benchmark: full count scan is a dead end for sub-second targets"
created: 2026-07-09
type: observation
status: seedling
source: "session 2026-07-09, luz_docs count-estimate research"
tags: [luz-docs, mongodb, performance, benchmark]
---

# luz_docs benchmark: full count scan is a dead end for sub-second targets

On a 128k-document tenant, luz_docs's /documents/count took 14.1s median as a single full scan, and still 3.15s median with the existing K=6 _shard fan-out parallelization — both far past a 500ms target. The fan-out's floor is MongoDB primary contention, not application thread starvation, so adding more parallel workers past that point doesn't help.

**Why it matters:** this rules out scanning — parallel or not — as a path to sub-second/sub-500ms counts at this scale. The only way to get there is to avoid scanning at query time altogether: a maintained/cached answer (epoch-keyed cache), an index-covered COUNT_SCAN via a materialized sentinel field, or a precomputed sketch (HyperLogLog). This is the fact that justified investigating HLL for luz_docs's estimated-count feature at all.

Related: [[luz_docs estimated-count POC drops CAS and backfill gate]], [[HyperLogLog small-range error needs linear-counting correction]].

## Related

- [[luz_docs estimated-count POC drops CAS and backfill gate]]
- [[HyperLogLog small-range error needs linear-counting correction]]
