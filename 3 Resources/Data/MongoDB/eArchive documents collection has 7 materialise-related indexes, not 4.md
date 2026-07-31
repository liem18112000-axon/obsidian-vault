---
title: "eArchive documents collection has 7 materialise-related indexes, not 4"
created: 2026-07-13
type: observation
status: seedling
source: "eArchive ops session 2026-07-13, tenant 45b05710 performance env"
tags: [mongodb, luz-docs, earchive, indexes]
---

# eArchive documents collection has 7 materialise-related indexes, not 4

The Luz eArchive `documents` collection actually carries **7** materialise-related secondary indexes in dev/performance, not the ~3-4 implied by the earchive-materialize-index skill's description (which only calls out `_isPublic`, `_effectiveSecurityClassCodes`, `_folderNames`, and an `_updatedDate` sort index).

Observed live index set on `documents` (tenant 45b05710-b9d4-4d3e-935e-83c4525369fa, performance env, 2026-07-13):
- `idx_isPublic_updatedDate`
- `idx_effectiveSecurityClassCodes_updatedDate`
- `idx_folderNames`
- `idx_effectiveSecurityClassCodes_shard`
- `idx_isPublic_shard`
- `idx_shard`
- `idx_trigrams` (backs `_searchTrigrams`, the substring-search sliding-window field)

So three extra shard-fanout compound indexes and one search-trigram index exist alongside the four documented sentinel-field indexes. Anyone auditing or rebuilding materialise indexes (or estimating index storage/write overhead) needs to account for all 7, not just the documented set.

**Update 2026-07-13 (dev canary tenant d0783310-d67f-4ab7-9aab-dcaef3f17f48):** a tenant that's actually been exercised by the real app (not just skill-seeded) carries **16** secondary indexes on `documents`, not 7 — the 7 above plus `idx_folderIds_updatedDate`, `idx_tags_updatedDate`, `idx_deletionStatus_updatedDate`, `idx_isStored_updatedDate`, `idx_markedAsUnread_updatedDate`, `idx_createdDate`, `idx_updatedDate`, `idx_effSCC_updatedDate_en`, `idx_folderNames_en` (English-locale variant of folder-name search). So the true count depends on how much production/app traffic has touched that tenant's DB — skill-only seeding only ever produces the smaller materialise-sentinel set; real usage accumulates query-pattern indexes (per-field `*_updatedDate` sort compounds, locale variants) on top. Treat "how many indexes" as tenant-history-dependent, not a fixed constant.

## Related

- [[Luz eArchive tenant mongo database collection list]]
