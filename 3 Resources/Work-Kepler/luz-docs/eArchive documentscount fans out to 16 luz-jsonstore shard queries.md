---
title: "eArchive documents/count fans out to 16 luz-jsonstore shard queries"
created: 2026-07-13
type: observation
status: seedling
source: "session 2026-07-13 eArchive api_calls capture"
tags: [luz-docs, earchive, performance, sharding]
---

# eArchive documents/count fans out to 16 luz-jsonstore shard queries

On dev, a single eArchive UI **`POST /luz_docs/api/{tenant}/documents/count`** does not become one Mongo count — luz-docs fans it out to **16** `POST luz-jsonstore .../documents/count` calls, one per `_shard` range. The only difference between the 16 bodies is the `_shard` clause (e.g. `{"_shard":{"$gte":805306368,"$lt":939524096}}`, `{"_shard":{"$gte":939524096}}`, ...); everything else (the big inbox filter + security-class `$in` list) is identical. Count cost therefore scales with shard count.

Related observations from the same eArchive load:
- **Slow path**: `POST documents/search?include-folder-name=true&exclude-total-count=true` averaged ~1.7 s (max ~2.6 s) — the per-document folder-name join dominates page-load wall clock.
- `GET documents/{id}/files/thumbnail128` returns **404** for synthetic seed data (no rendered file), wasting ~300 ms per doc.

## Related

- [[luz-docs API request bodies are only observable as downstream luz-jsonstore queries]]
