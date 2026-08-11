---
ai_hash: 5bb0f9a382fb2ffc
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-02
entities:
- Materialize bulk PATCH
- N serial per-doc PATCH calls
- Materialized tenant
- PATCH /luz_docs/api/{tenantId}/documents
- Mongo
- updateManyByFilter
- MaterializeFacade.tryMaterializeBulkPatch
- Optional<BulkDocumentOperationResponse>
- bulk handler
- MaterializePerDocPatchExecutor.execute
- DocumentService.updatePatchDocumentMetadata
- documentId
- getDocumentMetadataById
- loadFoldersById
- stampMaterializeOnPatch
- updatePatchMetadata
- shouldUseMaterialized(tenantId)
- cascadeService.touchesMaterializeFields(query)
- touchesMaterializeFields
- folderIds
- securityClassCodes
- _isPublic
- _effectiveSecurityClassCodes
- _folderNames
- updateMany pipeline
- Latency cliff
- Audit-log events
- Statistic-cache events
- MAX_UPDATE_MANY_ENTRIES
- Load Balancer (LB)
- ManagedExecutorService
- updateMany
- securityClassCodes scalar string breaks materialize sentinels
- flattenArrayAddOps runs only in materialize branch
- Missing folder reference produces fail-closed materialize state
- Materialize appendAsPatchOps uses RFC-6902 replace for sentinel fields
- 01 Overview - Folder Rename Cascade
source: code review of DocumentService.updateManyDocumentMetadataByPatch + MaterializeFacade
status: seedling
tags:
- luz-docs
- materialize
- performance
- bulk-patch
- gotcha
title: Materialize bulk PATCH fans out into N serial per-doc PATCH calls
type: lesson
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

%% ai-graph-start %%

**Related notes:**
- [[luz_docs bulk folder PATCH runs the materialize cascade once per entry]]
- [[Materialize appendAsPatchOps uses RFC-6902 replace for sentinel fields]]
- [[luz_docs folder security-class changes have 3 entry points but only PUT cascades]]
- [[luz_docs bulk updateMany recompute is set-based - one event, batched literal-table pipeline, not per-doc fan-out]]
- [[luz_docs onFolderParentsChange risk profile - sync fan-out, page-read gap, paging races]]

**Relations:**
- Materialize bulk PATCH — *fans out into* — N serial per-doc PATCH calls
- PATCH /luz_docs/api/{tenantId}/documents — *is a type of* — Materialize bulk PATCH
- Materialize bulk PATCH — *operates on* — Materialized tenant
- Materialize bulk PATCH — *does not use* — updateManyByFilter
- updateManyByFilter — *involves* — Mongo
- MaterializeFacade.tryMaterializeBulkPatch — *returns* — Optional<BulkDocumentOperationResponse>
- bulk handler — *uses* — MaterializePerDocPatchExecutor.execute
- MaterializePerDocPatchExecutor.execute — *calls* — DocumentService.updatePatchDocumentMetadata
- DocumentService.updatePatchDocumentMetadata — *processes* — documentId
- DocumentService.updatePatchDocumentMetadata — *performs* — getDocumentMetadataById
- DocumentService.updatePatchDocumentMetadata — *performs* — loadFoldersById
- DocumentService.updatePatchDocumentMetadata — *performs* — stampMaterializeOnPatch
- DocumentService.updatePatchDocumentMetadata — *performs* — updatePatchMetadata
- N serial per-doc PATCH calls — *involves* — read + read + write sequential round-trips
- updateManyByFilter — *is used on* — non-materialized tenant
- Materialize bulk PATCH — *triggered by* — shouldUseMaterialized(tenantId)
- Materialize bulk PATCH — *triggered by* — cascadeService.touchesMaterializeFields(query)
- touchesMaterializeFields — *matches* — folderIds
- touchesMaterializeFields — *matches* — securityClassCodes
- N serial per-doc PATCH calls — *necessary for* — _isPublic
- N serial per-doc PATCH calls — *necessary for* — _effectiveSecurityClassCodes
- N serial per-doc PATCH calls — *necessary for* — _folderNames
- _isPublic — *depends on* — current document state
- _isPublic — *depends on* — current folder state
- _effectiveSecurityClassCodes — *depends on* — current document state
- _effectiveSecurityClassCodes — *depends on* — current folder state
- _folderNames — *depends on* — current document state
- _folderNames — *depends on* — current folder state
- updateMany pipeline — *cannot read* — per-document state
- Materialized tenant — *experiences* — Latency cliff
- Materialized tenant — *causes* — Audit-log events
- Audit-log events — *to fire* — N times
- Materialized tenant — *causes* — Statistic-cache events
- Statistic-cache events — *to fire* — N times
- MAX_UPDATE_MANY_ENTRIES — *can cause* — request timeout
- request timeout — *at* — Load Balancer (LB)
- ManagedExecutorService — *can improve* — Parallelize per-doc work
- Group docs by sentinels — *can improve* — updateMany
- Stream response — *can improve* — partial progress
- Materialize bulk PATCH — *related to* — securityClassCodes scalar string breaks materialize sentinels
- Materialize bulk PATCH — *related to* — flattenArrayAddOps runs only in materialize branch
- Materialize bulk PATCH — *related to* — Missing folder reference produces fail-closed materialize state
- Materialize bulk PATCH — *related to* — Materialize appendAsPatchOps uses RFC-6902 replace for sentinel fields
- Materialize bulk PATCH — *related to* — 01 Overview - Folder Rename Cascade

%% ai-graph-end %%