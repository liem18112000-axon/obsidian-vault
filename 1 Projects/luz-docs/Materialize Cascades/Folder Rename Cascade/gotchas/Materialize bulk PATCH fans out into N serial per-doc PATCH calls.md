---
title: Materialize bulk PATCH fans out into N serial per-doc PATCH calls
created: 2026-06-02
type: lesson
status: seedling
source: code review of DocumentService.updateManyDocumentMetadataByPatch + MaterializeFacade
tags: [luz-docs, materialize, performance, bulk-patch, gotcha]
---

# Materialize bulk PATCH fans out into N serial per-doc PATCH calls

On a materialized tenant, `PATCH /luz_docs/api/{tenantId}/documents` (bulk) does **not** hit Mongo as one `updateManyByFilter`. Instead `MaterializeFacade.tryMaterializeBulkPatch` returns an `Optional<BulkDocumentOperationResponse>` and the bulk handler short-circuits to a per-entry loop in `MaterializePerDocPatchExecutor.execute`, which sequentially calls `DocumentService.updatePatchDocumentMetadata` once per `documentId`.

Each per-doc call performs:
1. `getDocumentMetadataById` (read)
2. `loadFoldersById` (read folders for cascade input)
3. `stampMaterializeOnPatch` (simulate + compute)
4. `updatePatchMetadata` (write)

So a 1 000-entry bulk on a materialized tenant = 1 000 × (read + read + write) sequential round-trips, vs a single `updateManyByFilter` on a non-materialized tenant.

## Trigger condition

```java
shouldUseMaterialized(tenantId) && cascadeService.touchesMaterializeFields(query)
```

`touchesMaterializeFields` matches when the **first path segment** of any op is `folderIds` or `securityClassCodes`. Catches `/folderIds`, `/folderIds/-`, `/folderIds/0`, `/securityClassCodes`, `/securityClassCodes/-`, etc.

## Why it's like this

Per-doc fan-out is necessary because the materialize sentinels (`_isPublic`, `_effectiveSecurityClassCodes`, `_folderNames`) depend on **current document + current folder state**, which a bulk `updateMany` pipeline cannot read per-document.

## Operational impact

- Latency cliff once `shouldUseMaterialized` flips true for a tenant.
- Audit-log and statistic-cache events fire N times instead of once — asymmetric metric volume vs non-materialized tenants.
- If `MAX_UPDATE_MANY_ENTRIES` is large, request can time out at the LB before the loop finishes.

## Possible improvements (not implemented)

- Parallelize per-doc work via a bounded `ManagedExecutorService`.
- Group docs by identical resulting sentinels and emit one `updateMany` per group.
- Stream the response so the client sees partial progress.

## Related

- [[securityClassCodes scalar string breaks materialize sentinels]]
- [[flattenArrayAddOps runs only in materialize branch]]
- [[Missing folder reference produces fail-closed materialize state]]
- [[Materialize appendAsPatchOps uses RFC-6902 replace for sentinel fields]]
- [[01 Overview - Folder Rename Cascade]]
