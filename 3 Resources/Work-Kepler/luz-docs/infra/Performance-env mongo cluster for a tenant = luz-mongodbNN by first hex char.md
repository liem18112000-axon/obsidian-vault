---
ai_hash: 3de7e02ef66f30c3
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-14
entities: []
source: session 2026-07-14 folderIds-facet investigation
status: seedling
tags:
- luz-docs
- mongodb
- performance-env
- kubernetes
- earchive
title: Performance-env mongo cluster for a tenant = luz-mongodbNN by first hex char
type: reference
---

# Performance-env mongo cluster for a tenant = luz-mongodbNN by first hex char

On the **Performance** environment (GCP project/cluster `klara-performance`, distinct from dev), a tenant`\s MongoDB replica set is selected by the **first hex character of the tenant id**: cluster index `NN` = that hex char, giving `luz-mongodbNN-cluster-rs-*` in namespace `performance-mongodb-clusters`.

Confirmed example: tenant `45b05710-...` (first hex char `4`) → **`luz-mongodb04-cluster-rs-2`**. This confirms the perf-env cluster rule (perf has 16 replica sets, one per hex value).

Observed on that tenant`\s `documents` collection: ~800k docs, ~12.6GB, avgObjSize ~15.8KB. Indexes present: `_id_`, `idx_updatedDate`, `idx_shard`, `idx_isPublic_shard`, `idx_effectiveSecurityClassCodes_shard`. **`folderIds` has NO index**, and the `earchive-materialize-index` target set (`_isPublic`, `_effectiveSecurityClassCodes`, `_folderNames`, `_updatedDate`, `_shard`) **omits `folderIds` entirely** — so the folderIds facet COLLSCANs. See [[luz-docs folderIds facet 500 is a Mongo $group memory-limit abort (error 292)]].

## Related

- [[luz-docs folderIds facet 500 is a Mongo $group memory-limit abort (error 292)]]

%% ai-graph-start %%

**Related notes:**
- [[Luz performance env cluster topology]]
- [[Luz eArchive tenant mongo database collection list]]
- [[eArchive documents collection has 7 materialise-related indexes, not 4]]
- [[luz-docs documentscount is ~130s on an 800k tenant — the 16-shard fan-out, not counting, is the bottleneck]]
- [[Canary tenant eArchive folder list trips Mongo code 292 sort-memory-limit]]

%% ai-graph-end %%