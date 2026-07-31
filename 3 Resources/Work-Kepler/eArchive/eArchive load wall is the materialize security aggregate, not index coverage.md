---
title: "eArchive load wall is the materialize security aggregate, not index coverage"
created: 2026-06-26
type: observation
status: seedling
source: "session 2026-06-26 index A/B benchmark"
tags: [klara, earchive, mongodb, index, benchmark, materialize, performance]
---

# eArchive load wall is the materialize security aggregate, not index coverage

A/B benchmark on dev (tenant `d0783310…`, 128k docs, 10 runs each) compared **no indexes vs a_vu (6 indexes) vs liem (15 indexes)** on the eArchive page. The dominant cost — `letters/security-class-codes` (~27 s) and its underlying `documents/aggregate` (~6 s) — **did not improve in any suite**; liem was even marginally worse. This restricted-visibility `$in` materialize aggregate is not served by `_effectiveSecurityClassCodes` or its collated `_en` twin at scale.

**Conclusion:** eArchive load time is bounded by the materialize security aggregate, which is **index-immune**. Fixing it needs a code/aggregate-shape change (or a collation-matched index the aggregate's planner actually selects), not more index coverage.

**What indexes DID win (liem):** the cheap count/list/projection family collapsed — `documents/count` 2,301→575 ms, VC `badge-count` 3,729→249 ms (~15×), `documents/list(48)` 1,165→~35 ms, `securityProj` 1,710→~340 ms. Keep these.

**Regressions to watch (liem):** `documents/search` and `with-count` went ~760 → ~2,700 ms — planner picking a worse index for those shapes; needs an `explain` follow-up.

**a_vu** was the only case to improve folder-drill (12.1→9.2 s) and security-class-codes (27.5→22.4 s), but lost the cheap-count gains — a different trade, still no aggregate fix.

Reports: `luz_docs/docs/index-earchive/benchmark/{no_index,a_vu,liem,comparison}_report.md`.

## Related

- [[JSF LetterStorageDetail instance URL dies mid-benchmark; remint via eArchive menu]]
- [[reference_mongo_collation_index]]
