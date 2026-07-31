---
title: "luz_docs estimated-count POC drops CAS and backfill gate"
created: 2026-07-09
type: lesson
status: seedling
source: "session 2026-07-09"
tags: [luz-docs, hyperloglog, poc, scope-reduction]
---

# luz_docs estimated-count POC drops CAS and backfill gate

luz_docs shipped its HyperLogLog-based estimated document count as a POC with two safety mechanisms deliberately removed: optimistic-concurrency (CAS + version field on the sketch write) and a backfill-complete gate.

**Why:** the user's stated goal narrowed mid-implementation from production-readyestimatemode to proveHLLcountsfaster — CAS and the gate protect against a lost-update race (two pods writing the same folder's sketch concurrently) and against estimating on a not-yet-backfilled tenant, respectively. Neither risk threatens the POC's actual question (can a HyperLogLog sketch answer count>N fast), so both were cut to keep the diff small: [[EstimatedCountSketchRepository.write is now a plain ]] instead of a findAndModify CAS write, and there is no isBackfillComplete check — a missing sketch just falls back to the exact count.

**How to apply:** when a user scope-reduces a feature to "just prove X" mid-build, look for the concurrency-safety and rollout-safety layers first — those are usually the parts that exist to protect production correctness, not the core mechanism being proven, and are the cheapest things to strip for a POC. The stripped mechanisms should stay documented (not deleted) in the original design doc as the path back to production-hardening — see the implementation plan doc's CAS/gate design, now stale against the POC code but kept on purpose.

Related: [[3 Resources/Data/Algorithms/HyperLogLog error in the small-range (linear-counting) regime]], [[luz_docs benchmark: full count scan is a dead end for sub-second targets]].

## Related

- [[luz_docs count optimization]]
