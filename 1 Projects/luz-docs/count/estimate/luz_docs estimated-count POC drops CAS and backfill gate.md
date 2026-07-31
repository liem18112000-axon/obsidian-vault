---
ai_hash: 0efde0b6284a47cc
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-09
entities:
- luz_docs
- estimated-count POC
- CAS
- backfill gate
- HyperLogLog
- document count
- optimistic-concurrency
- version field
- sketch write
- production-ready estimatemode
- proveHLLcountsfaster
- lost-update race
- EstimatedCountSketchRepository.write
- findAndModify CAS write
- isBackfillComplete check
- exact count
- concurrency-safety
- rollout-safety
- implementation plan doc
- CAS/gate design
- HyperLogLog error in the small-range (linear-counting) regime
- 'luz_docs benchmark: full count scan is a dead end for sub-second targets'
- luz_docs count optimization
- safety mechanisms
- HLL
source: session 2026-07-09
status: seedling
tags:
- luz-docs
- hyperloglog
- poc
- scope-reduction
title: luz_docs estimated-count POC drops CAS and backfill gate
type: lesson
---

# luz_docs estimated-count POC drops CAS and backfill gate

luz_docs shipped its HyperLogLog-based estimated document count as a POC with two safety mechanisms deliberately removed: optimistic-concurrency (CAS + version field on the sketch write) and a backfill-complete gate.

**Why:** the user's stated goal narrowed mid-implementation from production-readyestimatemode to proveHLLcountsfaster — CAS and the gate protect against a lost-update race (two pods writing the same folder's sketch concurrently) and against estimating on a not-yet-backfilled tenant, respectively. Neither risk threatens the POC's actual question (can a HyperLogLog sketch answer count>N fast), so both were cut to keep the diff small: [[EstimatedCountSketchRepository.write is now a plain ]] instead of a findAndModify CAS write, and there is no isBackfillComplete check — a missing sketch just falls back to the exact count.

**How to apply:** when a user scope-reduces a feature to "just prove X" mid-build, look for the concurrency-safety and rollout-safety layers first — those are usually the parts that exist to protect production correctness, not the core mechanism being proven, and are the cheapest things to strip for a POC. The stripped mechanisms should stay documented (not deleted) in the original design doc as the path back to production-hardening — see the implementation plan doc's CAS/gate design, now stale against the POC code but kept on purpose.

Related: [[3 Resources/Data/Algorithms/HyperLogLog error in the small-range (linear-counting) regime]], [[luz_docs benchmark: full count scan is a dead end for sub-second targets]].

## Related

- [[luz_docs count optimization]]

%% ai-graph-start %%

**Related notes:**
- [[luz_docs documentscount is scan-bound and cannot reach sub-second at 128k]]
- [[luz_docs countN badge can use HyperLogLog with a fuzzy-zone fallback]]
- [[luz_docs runs non-clustered WildFly pods, so pod-local sketchcounter state is broken]]
- [[Per-document backfill executors assume no shared write target]]
- [[Shared aggregate write targets need CAS, not plain $set]]

**Relations:**
- luz_docs — *shipped* — estimated-count POC
- estimated-count POC — *uses* — HyperLogLog
- estimated-count POC — *is for* — document count
- estimated-count POC — *removed* — CAS
- estimated-count POC — *removed* — backfill gate
- CAS — *is a type of* — optimistic-concurrency
- optimistic-concurrency — *involves* — version field
- optimistic-concurrency — *involves* — sketch write
- CAS — *is a* — safety mechanisms
- backfill gate — *is a* — safety mechanisms
- user's stated goal — *narrowed to* — proveHLLcountsfaster
- original goal — *was* — production-ready estimatemode
- CAS — *protects against* — lost-update race
- backfill gate — *protects against* — estimating on a not-yet-backfilled tenant
- lost-update race — *involves* — two pods writing the same folder's sketch concurrently
- HyperLogLog sketch — *answers* — count>N fast
- EstimatedCountSketchRepository.write — *is now* — plain
- EstimatedCountSketchRepository.write — *was* — findAndModify CAS write
- missing sketch — *falls back to* — exact count
- isBackfillComplete check — *is not* — present
- concurrency-safety — *is a type of* — layer
- rollout-safety — *is a type of* — layer
- stripped mechanisms — *should stay documented in* — implementation plan doc
- implementation plan doc — *contains* — CAS/gate design
- CAS/gate design — *is* — stale against the POC code
- luz_docs — *related to* — HyperLogLog error in the small-range (linear-counting) regime
- luz_docs — *related to* — luz_docs benchmark: full count scan is a dead end for sub-second targets
- luz_docs — *related to* — luz_docs count optimization
- HyperLogLog — *is also known as* — HLL

%% ai-graph-end %%