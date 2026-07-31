---
title: "Mongo facet $group count: index only helps the $match prefix, not the count"
created: 2026-06-26
type: lesson
status: seedling
source: "session 2026-06-26 luz-docs searchByFacets analysis"
tags: [mongodb, luz-docs, aggregation, gotcha, performance]
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
