---
ai_hash: 387e3a96fbe6fe40
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-09
entities:
- luz_docs
- Kepler
- count>N UI badge
- HyperLogLog
- fuzzy-zone fallback
- 500ms target
- 128k+ docs
- cosmetic threshold badge
- precomputed dimensions
- folder id
- tag
- security-class code
- _isPublic
- materialize pipeline
- arbitrary/ad hoc/free-text filter
- index
- fuzzy zone
- exact capped count
- security-facing visible-document count
- HyperLogLog error
- docs/bitmap-count-investigation.md
- docs/perf-LUZ-154613-count-scaling-findings-and-solution.md
- HyperLogLog error in the small-range (linear-counting) regime
- luz_docs documentscount
source: luz_docs count-estimate research, 2026-07-09, branch kepler/sprint-159/LUZ-154613-shard-adapt-migraiton
status: seedling
tags:
- luz-docs
- hyperloglog
- kepler
- count-optimization
- design-decision
title: luz_docs count>N badge can use HyperLogLog with a fuzzy-zone fallback
type: observation
---

# luz_docs count>N badge can use HyperLogLog with a fuzzy-zone fallback

For luz_docs (Kepler), the question was whether a `count(query) > N` UI badge (e.g. flipping to "999+") could use a HyperLogLog sketch to stay under a 500ms target even at 128k+ docs.

Decision: HLL is a legitimate fit for this *cosmetic* threshold badge, but only under two conditions:

1. **The predicate must decompose into a small, fixed set of precomputed dimensions** (folder id, tag, security-class code, `_isPublic`) — each maintained as its own HLL sketch, updated O(1) per write (same write-path pattern already used for `_isPublic`/`_effectiveSecurityClassCodes`/`_shard` in the materialize pipeline). A merged HLL only answers "how many docs are in the union of these precomputed sketches" — it cannot handle an arbitrary/ad hoc/free-text filter, because you can't precompute a sketch per every string a user might type, and building one at query time costs the same scan you were trying to avoid. Ad hoc full-text counts need an index ($text or trigram field), not a sketch.

2. **The estimate-vs-N comparison must use a fuzzy zone**, not a raw point comparison — see [[HyperLogLog error in the small-range (linear-counting) regime]] for why a single estimate can't safely decide a close boundary. E.g.: estimate < 0.95N -> show as-is; estimate > 1.05N -> show "N+"; in between -> fall back to an exact capped count (`limit: N+1`).

This is a narrowing of an existing, already-decided rule in the luz_docs codebase (see docs/bitmap-count-investigation.md and docs/perf-LUZ-154613-count-scaling-findings-and-solution.md): HLL is explicitly *rejected* for the security-facing visible-document count, because its ~1-2% error is access-leak-shaped (could under-count and hide a document a user should see, or the reverse). A cosmetic ">999" display badge is a different, lower-stakes question than "can this user see this document" — that's what makes HLL acceptable here when it wasn't for the security count.

## Related

- [[HyperLogLog error in the small-range (linear-counting) regime]]
- [[1 Projects/luz-docs/luz_docs documentscount is scan-bound and cannot reach sub-second at 128k]]

%% ai-graph-start %%

**Related notes:**
- [[luz_docs documentscount is scan-bound and cannot reach sub-second at 128k]]
- [[HyperLogLog error in the small-range (linear-counting) regime]]
- [[Visible-document count as cardinality of a bitmap union]]
- [[HyperLogLog estimates distinct count in constant memory and is mergeable]]
- [[luz_docs estimated-count POC drops CAS and backfill gate]]

**Relations:**
- luz_docs — *is also known as* — Kepler
- count>N UI badge — *is for* — luz_docs
- count>N UI badge — *can use* — HyperLogLog
- count>N UI badge — *can use* — fuzzy-zone fallback
- HyperLogLog — *aims for* — 500ms target
- HyperLogLog — *scales to* — 128k+ docs
- count>N UI badge — *is a type of* — cosmetic threshold badge
- HyperLogLog — *requires* — precomputed dimensions
- precomputed dimensions — *include* — folder id
- precomputed dimensions — *include* — tag
- precomputed dimensions — *include* — security-class code
- precomputed dimensions — *include* — _isPublic
- _isPublic — *is updated in* — materialize pipeline
- HyperLogLog — *cannot handle* — arbitrary/ad hoc/free-text filter
- arbitrary/ad hoc/free-text filter — *requires* — index
- fuzzy zone — *is used for* — estimate-vs-N comparison
- fuzzy zone — *can trigger* — exact capped count
- HyperLogLog — *has* — HyperLogLog error
- HyperLogLog error — *is* — ~1-2% error
- ~1-2% error — *is* — access-leak-shaped
- HyperLogLog — *is rejected for* — security-facing visible-document count
- HyperLogLog — *is acceptable for* — cosmetic threshold badge
- HyperLogLog error — *is detailed in* — HyperLogLog error in the small-range (linear-counting) regime
- luz_docs — *has related document* — docs/bitmap-count-investigation.md
- luz_docs — *has related document* — docs/perf-LUZ-154613-count-scaling-findings-and-solution.md
- luz_docs documentscount — *is related to* — luz_docs
- luz_docs documentscount — *is* — scan-bound

%% ai-graph-end %%