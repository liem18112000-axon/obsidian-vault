---
title: "Performance-env mongo cluster for a tenant = luz-mongodbNN by first hex char"
created: 2026-07-14
type: reference
status: seedling
source: "session 2026-07-14 folderIds-facet investigation"
tags: [luz-docs, mongodb, performance-env, kubernetes, earchive]
---

# Performance-env mongo cluster for a tenant = luz-mongodbNN by first hex char

On the **Performance** environment (GCP project/cluster `klara-performance`, distinct from dev), a tenant`\s MongoDB replica set is selected by the **first hex character of the tenant id**: cluster index `NN` = that hex char, giving `luz-mongodbNN-cluster-rs-*` in namespace `performance-mongodb-clusters`.

Confirmed example: tenant `45b05710-...` (first hex char `4`) → **`luz-mongodb04-cluster-rs-2`**. This confirms the perf-env cluster rule (perf has 16 replica sets, one per hex value).

Observed on that tenant`\s `documents` collection: ~800k docs, ~12.6GB, avgObjSize ~15.8KB. Indexes present: `_id_`, `idx_updatedDate`, `idx_shard`, `idx_isPublic_shard`, `idx_effectiveSecurityClassCodes_shard`. **`folderIds` has NO index**, and the `earchive-materialize-index` target set (`_isPublic`, `_effectiveSecurityClassCodes`, `_folderNames`, `_updatedDate`, `_shard`) **omits `folderIds` entirely** — so the folderIds facet COLLSCANs. See [[luz-docs folderIds facet 500 is a Mongo $group memory-limit abort (error 292)]].

## Related

- [[luz-docs folderIds facet 500 is a Mongo $group memory-limit abort (error 292)]]
