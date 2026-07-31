---
ai_hash: b16dadf5e5ec1d3b
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-26
entities: []
source: session 2026-06-26 luz-docs searchByFacets analysis
status: seedling
tags:
- mongodb
- luz-docs
- aggregation
- gotcha
- performance
title: 'Mongo facet $group count: index only helps the $match prefix, not the count'
type: lesson
---

# Mongo facet $group count: index only helps the $match prefix, not the count

In a MongoDB facet-count aggregation (`\$group` with `count:{\$sum:1}`), an index can only speed up the **leading `\$match` prefix** — the stages before the first `\$group`/`\$unwind`/`\$lookup`. The count grouping itself is O(matched docs) and is never index-served, and any `\$sort` placed after `\$group` runs in memory on grouped output (also unindexable).

## Why it matters
This bounds how much a "correct" index can help a facet endpoint. Confirmed in luz-docs `searchByFacets` → `getFacets` (JsonStoreMongoService.java:556): the pipeline is `[\$match ...][\$lookup folders][\$match][\$unwind?][\$group count:{\$sum:1}][\$sort][\$skip][\$limit]`.

- **Selective leading `\$match`** (narrow filter / security-class subset) → big win: a collated compound index avoids COLLSCAN and feeds a small set to `\$group`.
- **Compound index covering match fields + the facet field** → covered IXSCAN feeds `\$group` without FETCHing full BSON = a constant-factor win, **not** asymptotic.
- **Low-selectivity facet** (count-all-by-field, e.g. `deletionStatus:false` ≈ all docs) → `\$group` traverses ~every doc; IXSCAN over ~all keys ≈ COLLSCAN cost. Index ≈ no win. Real lever = materialize/precompute or count-fanout.
- **Non-materialized security path** adds a `\$lookup` on folders (per-doc join, unprunable on the documents side) — the reason the materialize path (`_effectiveSecurityClassCodes`) was built to drop the `\$lookup`.

## Dominant gotcha
The pipeline runs with `COLLATION_DEFAULT = {locale:"en", caseFirst:"UPPER"}`. A string index is usable only if it carries the **same** collation, else Mongo silently skips it → COLLSCAN. See [[Mongo collated query needs a same-collation index or it falls back to COLLSCAN]].

## Verify, do not assume
`explain("executionStats")`: leading stage must be `IXSCAN` (not `COLLSCAN`), `totalDocsExamined` ≈ docs feeding `\$group`, and the chosen index collation matches `{locale:en,caseFirst:UPPER}`.

## Related
[[Mongo collated query needs a same-collation index or it falls back to COLLSCAN]]

## Related

- [[Mongo collated query needs a same-collation index or it falls back to COLLSCAN]]

%% ai-graph-start %%

**Related notes:**
- [[MongoDB $facet buckets add no parallelism and defeat COUNT_SCAN]]
- [[Production security count is already COUNT_SCAN (covered); benchmark query's FETCH is inherent (multikey+$or+$nin)]]
- [[Count fan-out _shard index must put _shard LAST in the compound key (ESR)]]
- [[Partition the materialized count on a uniform _countShard int, not _id]]
- [[_shard fan-out uses idx_shard (IXSCAN exact slice); local port-forward masks the speedup]]

%% ai-graph-end %%