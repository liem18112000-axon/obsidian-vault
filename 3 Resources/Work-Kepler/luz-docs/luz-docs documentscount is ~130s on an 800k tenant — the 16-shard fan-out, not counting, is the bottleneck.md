---
title: "luz-docs /documents/count is ~130s on an 800k tenant — the 16-shard fan-out, not counting, is the bottleneck"
created: 2026-07-14
type: observation
status: seedling
source: "session 2026-07-14 performance benchmark"
tags: [luz-docs, earchive, performance, sharding, mongodb]
---

# luz-docs /documents/count is ~130s on an 800k tenant — the 16-shard fan-out, not counting, is the bottleneck

Benchmarked on the Performance eArchive tenant `45b05710-…` (800,000 docs, all `_isPublic:true`, evenly spread across 16 `_shard` buckets), direct REST via api-forwarder, 11 trials each.

**`POST /luz_docs/api/{tenant}/documents/count` is catastrophically slow: ~120–178 s** for every filter body (inbox / archive / single-folder), with **no warm speedup** (cold ≈ warm). It is **mongo-bound** — during count runs luz-docs sat near-idle (~50–110 mcores) while mongo primary CPU ran 700–1500 mcores. Cause: the endpoint fans a count out to all **16 `_shard` ranges**, each scanning ~50k docs.

Key contrast that isolates the culprit: counting the *same folder* via `POST documents/search?exclude-total-count=false` returns the total in **~1.4 s** — ~90× faster than the standalone `/documents/count` (~133 s). So the slowness is the count **endpoint/fan-out implementation**, not the act of counting. Optimisation lever: make `/documents/count` use the cheaper search total-count path (or cache/estimate), not 16 per-shard scans.

Operational: sustained counts dont shed load gracefully — after 6 consecutive ~141 s counts the endpoint returned **502 Bad Gateway ×3 then 400 ×2** (a luz-docs pod overloaded/recycled).

By contrast, list reads are fine: `documents/search` with `exclude-total-count=true` (indexed `_updatedDate` sort + `limit 48`) ran ~1.2–1.9 s; `folders/search` and `GET folders|documents/{id}` ~1.1 s warm (one 39 s cold-cache spike on first doc read).

See also [[eArchive documents/count fans out to 16 luz-jsonstore shard queries]]. Full data: `luz_docs/docs/performance-test-800k/test.md`.

## Related

- [[eArchive documents/count fans out to 16 luz-jsonstore shard queries]]
