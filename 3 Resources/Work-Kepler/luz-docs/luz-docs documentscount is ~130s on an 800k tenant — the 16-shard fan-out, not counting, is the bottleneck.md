---
ai_hash: e49c75e24455a67e
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-14
entities: []
source: sessions 2026-07-13 eArchive api_calls capture, 2026-07-14 performance benchmark
status: seedling
tags:
- luz-docs
- earchive
- performance
- sharding
- mongodb
title: luz-docs /documents/count is ~130s on an 800k tenant — the 16-shard fan-out,
  not counting, is the bottleneck
type: observation
---

# luz-docs /documents/count is ~130s on an 800k tenant — the 16-shard fan-out, not counting, is the bottleneck

**Mechanism:** one UI `POST /luz_docs/api/{tenant}/documents/count` is not one Mongo count — luz-docs fans it out to **16 `POST luz-jsonstore …/documents/count` calls**, one per `_shard` range. The 16 bodies are identical except the `_shard` clause (`{"_shard":{"$gte":805306368,"$lt":939524096}}`, … last one open-above). Count cost therefore scales with shard count.

**Cost, measured** on the Performance eArchive tenant `45b05710-…` (800k docs, all `_isPublic:true`, evenly spread over the 16 buckets), direct REST via api-forwarder, 11 trials: **~120–178 s** for every filter body (inbox / archive / single-folder), **no warm speedup** (cold ≈ warm). It is **mongo-bound** — luz-docs sat at ~50–110 mcores while the mongo primary ran 700–1500 mcores; each of the 16 sub-counts scans ~50k docs.

**Contrast that isolates the culprit:** counting the *same folder* via `POST documents/search?exclude-total-count=false` returns the total in **~1.4 s** — ~90× faster than `/documents/count` (~133 s). The slowness is the count **endpoint/fan-out implementation**, not counting itself. Optimisation lever: route `/documents/count` through the cheaper search total-count path (or cache/estimate) instead of 16 per-shard scans.

**Operational:** counts do not shed load gracefully — after 6 consecutive ~141 s counts the endpoint returned **502 ×3 then 400 ×2** (luz-docs pod overloaded/recycled).

**By contrast list reads are fine:** `documents/search` with `exclude-total-count=true` (indexed `_updatedDate` sort, `limit 48`) ~1.2–1.9 s; `folders/search` and `GET folders|documents/{id}` ~1.1 s warm (one 39 s cold-cache spike on first doc read). Two adjacent dev observations from the same eArchive load: `documents/search?include-folder-name=true` averaged ~1.7 s (the per-document folder-name join dominates page-load wall clock), and `GET documents/{id}/files/thumbnail128` 404s on synthetic seed data, wasting ~300 ms per doc.

Full data: `luz_docs/docs/performance-test-800k/test.md`.

## Related

- [[luz-docs API request bodies are only observable as downstream luz-jsonstore queries]]
- [[Luz K count-partitions env var]]
- [[Visible-document count as cardinality of a bitmap union]]

%% ai-graph-start %%

**Related notes:**
- [[eArchive count baseline latency on dev ~80s for 128k docs (fan-out off)]]
- [[eArchive 800k bottleneck is view-controller not K]]
- [[luz_docs documentscount is scan-bound and cannot reach sub-second at 128k]]
- [[Production security count is already COUNT_SCAN (covered); benchmark query's FETCH is inherent (multikey+$or+$nin)]]
- [[eArchive request flow and log correlation (perf)]]

%% ai-graph-end %%