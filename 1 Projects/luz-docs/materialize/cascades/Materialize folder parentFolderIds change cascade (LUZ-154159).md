---
title: "Materialize folder parentFolderIds change cascade (LUZ-154159)"
created: 2026-06-03
type: howto
status: seedling
source: "luz_docs branch kepler/sprint-158/LUZ-154159 HEAD a2f600488"
tags: [luz-docs, materialize, cascade, parent-folder-change, LUZ-154159, sprint-158]
---

# Materialize folder parentFolderIds change cascade (LUZ-154159)

**Ticket:** LUZ-154159 · **Branch:** `kepler/sprint-158/LUZ-154159-...-cascade-changes-update-folder-put-change-folder-parent-folder-ids` · **PR:** https://bitbucket.org/axonivy-prod/luz_docs/pull-requests/1322

## Problem

When a folder's `parentFolderIds` changes (folder moved in the hierarchy), the folder's `inheritedSecurityClassCodes` chain changes. Documents in the moved subtree carry materialised sentinel fields (`_isPublic`, `_effectiveSecurityClassCodes`, `_folderNames`, `_folderSecurityClassCodes`) that were computed against the OLD chain. Without a cascade, doc security visibility on the eArchive materialise read path drifts from truth.

## Decisions (locked in this session)

- **Trigger only on PUT** (`FolderService.updateFolderMetadata`). PATCH wiring intentionally not added in this ticket.
- **Docs-only cascade.** Caller (FolderService) is responsible for updating descendant folders' `inheritedSecurityClassCodes` BEFORE invoking the cascade. The cascade trusts the folder collection state.
- **Sync inline** (no async, no pub/sub, no marker-backed retry queue).
- **Single Mongo aggregation `updateMany` pipeline** computes the 4 sentinels server-side via `$lookup` against the post-update folder state. Pipeline is idempotent — re-running on already-correct docs is a no-op.
- **`@Retry` + `@Fallback`** at the service layer (MicroProfile Fault Tolerance). 3 retries × 500ms delay. Fallback restores from a snapshot and throws `FolderException(500)`.
- **Snapshot captured ONCE in the outer entry method**, not inside the retried method. See [[Snapshot for rollback must live outside retry boundary]].
- **CDI self-injection** routes the retried method through the proxy so the interceptor fires. See [[CDI self-invocation bypasses interceptor proxy]].

## Components

| File | Role |
|---|---|
| `FolderService.updateFolderMetadata` | PUT handler. Detects parentFolderIds change (old vs new metadata), computes affected folder subtree, calls `materializeFacade.onFolderParentChange`. |
| `MaterializeFacade.shouldCascadeFolderParent` / `onFolderParentChange` | Guard + delegation to `MaterializeFolderParentChangeService`. |
| `MaterializeFolderParentChangeService.onFolderParentChange` | Outer entry: snapshot → call `self.cascadeWithRetry` → `finally { deleteSnapshot }`. |
| `MaterializeFolderParentChangeService.cascadeWithRetry` | `@Retry` + `@Fallback`. Delegates to repository. |
| `MaterializeFolderParentChangeService.onCascadeFailed` | Fallback. Restores snapshot, throws `FolderException(500)`. |
| `MaterializeRepository.snapshotMaterializeStateForFolders` | Read affected docs (project the 4 sentinels), insert snapshot row into `materializeCascadeSnapshot` collection, return `_id`. |
| `MaterializeRepository.cascadeFolderParentChangeInDocuments` | `void`, throws `MaterializeCascadeException` on any failure (incl. 207). Single `updateMany` with aggregation pipeline. |
| `MaterializeRepository.restoreMaterializeStateFromSnapshot` | Read snapshot row, iterate `docs[]`, per-doc `setCollectionFields` via `restoreEachDocumentBySnapshotId`. |
| `MaterializeRepository.restoreEachDocumentBySnapshotId` | Per-doc restore. `@Retry` on `MaterializeCascadeException`. Called via `self.*` for proxy routing. |
| `MaterializeRepository.deleteSnapshot` | Best-effort cleanup. Failure logs WARNING + swallows (snapshot orphaned but does not mask original outcome). |
| `MaterializeQueryBuilder.buildFolderParentChangeFilter` | `{folderIds: {$in: [...affectedIds]}}`. |
| `MaterializeQueryBuilder.buildFolderParentChangePipeline` | 5-stage aggregation: `$lookup folders → $addFields ordered → $addFields per-folder names+codes → $addFields effective+isPublic → $unset temps`. |
| `MaterializeQueryBuilder.buildSnapshotRow` | `{affectedFolderIds, createdAt, docs}` row factory. |
| `MaterializeQueryBuilder.buildSentinelRestoreFields` | 4-field restore body factory from snapshot doc. |

## Flow

```
FolderService.updateFolderMetadata(PUT)
   ↓ (parentFolderIds changed?)
materializeFacade.onFolderParentChange(token, tenantId, affectedSubtree)
   ↓
service.onFolderParentChange  (outer entry)
   ├─ snapshot = repo.snapshotMaterializeStateForFolders(...)
   ├─ try
   │    └─ self.cascadeWithRetry(token, tenantId, affected, snapshotId)
   │         ├─ attempt 1: repo.cascadeFolderParentChangeInDocuments(...)
   │         │     ├─ ok → return
   │         │     └─ throws MaterializeCascadeException → @Retry waits 500ms
   │         ├─ attempts 2..3 (same path)
   │         └─ all attempts failed → @Fallback fires
   │              └─ onCascadeFailed
   │                   ├─ repo.restoreMaterializeStateFromSnapshot(snapshotId)
   │                   │     └─ forEach doc: self.restoreEachDocumentBySnapshotId  (own @Retry)
   │                   └─ throw FolderException(500)
   └─ finally
        └─ repo.deleteSnapshot(snapshotId)   (best-effort, log-on-fail)
```

## Mongo filter (`buildFolderParentChangeFilter`)

```json
{
  "folderIds": {
    "$in": ["<affectedFolderId-1>", "<affectedFolderId-2>", "..."]
  }
}
```

Matches any document whose `folderIds` array contains at least one element from the affected-subtree set.

## Mongo pipeline (`buildFolderParentChangePipeline`)

```
[
  // 1 — pull current folder state for each doc.folderIds
  {$lookup: {from: "folders", localField: "folderIds", foreignField: "_id", as: "__folders"}},

  // 2 — re-order $lookup output to match folderIds positions ($lookup loses order)
  {$addFields: {__ordered: {$map: {
    input: "$folderIds", as: "fid",
    in: {$arrayElemAt: [{$filter: {input: "$__folders", as: "f", cond: {$eq: ["$$f._id", "$$fid"]}}}, 0]}
  }}}},

  // 3 — per-folder name + per-folder union(own ∪ inherited)
  {$addFields: {
    _folderNames: {$map: {input: "$__ordered", as: "f", in: {$ifNull: ["$$f.name", ""]}}},
    _folderSecurityClassCodes: {$map: {input: "$__ordered", as: "f", in: {$setUnion: [
      {$ifNull: ["$$f.securityClassCodes", []]},
      {$ifNull: ["$$f.inheritedSecurityClassCodes", []]}
    ]}}}
  }},

  // 4 — effective (doc own ∪ all-folders union) + isPublic (no own codes AND any folder is fully open)
  {$addFields: {
    _effectiveSecurityClassCodes: {$setUnion: [
      {$ifNull: ["$securityClassCodes", []]},
      {$reduce: {input: "$_folderSecurityClassCodes", initialValue: [], in: {$setUnion: ["$$value", "$$this"]}}}
    ]},
    _isPublic: {$and: [
      {$eq: [{$size: {$ifNull: ["$securityClassCodes", []]}}, 0]},
      {$or: [
        {$eq: [{$size: {$ifNull: ["$folderIds", []]}}, 0]},
        {$anyElementTrue: {$map: {input: "$__ordered", as: "f", in: {$and: [
          {$eq: [{$size: {$ifNull: ["$$f.securityClassCodes", []]}}, 0]},
          {$eq: [{$size: {$ifNull: ["$$f.inheritedSecurityClassCodes", []]}}, 0]}
        ]}}}}
      ]}
    ]}
  }},

  // 5 — strip lookup temps
  {$unset: ["__folders", "__ordered"]}
]
```

Mirrors `MaterializeCompute.compute` Java algorithm 1:1.

## Snapshot row schema (`materializeCascadeSnapshot` collection)

```json
{
  "_id": "<ObjectId>",
  "affectedFolderIds": ["f1","f2","..."],
  "createdAt": "2026-06-03T07:42:14+07:00",
  "docs": [
    {"_id": "<docId>", "_isPublic": true, "_effectiveSecurityClassCodes": ["..."], "_folderNames": ["..."], "_folderSecurityClassCodes": [["..."]]}
  ]
}
```

One row per cascade attempt. Captured BEFORE pipeline runs. Read on fallback. Deleted in outer `finally`.

## Status-code mapping

| Outcome | Repository | Service |
|---|---|---|
| 200 OK | return | log complete |
| 207 multi-status | throws `MaterializeCascadeException` (wraps `DocumentException` w/ status 207) | retried |
| 4xx/5xx other | throws `MaterializeCascadeException` | retried |
| Transient transport (`ProcessingException`, `SocketTimeoutException`) | throws `MaterializeCascadeException` | retried |
| `NullPointerException` / `IllegalArgumentException` | propagates | `@Retry abortOn` — skip retry, fire fallback immediately |

## Test coverage

- `shouldCascadeFolderParent` truth table (same Set, different Set, empty, null)
- `onFolderParentChange` empty/null set short-circuit
- `onFolderParentChange` happy path: snapshot taken, cascade called, snapshot deleted in finally
- `onFolderParentChange` exception path: snapshot deleted in finally, exception propagates
- `cascadeFolderParentChangeInDocuments` 200 / 207 / transient timeout / filter shape
- `restoreEachDocumentBySnapshotId` happy path + missing fields default to empty arrays
- `deleteSnapshot` no-op on empty/null id, error swallowed
- QueryBuilder filter `$in` shape + pipeline 5-stage structure

236/236 tests green at HEAD `a2f600488`.

## Related

- [[CDI self-invocation bypasses interceptor proxy]] — why `@Inject self` is required
- [[Snapshot for rollback must live outside retry boundary]] — why snapshot lives in outer method
- LUZ-154586 GET-document-by-id materialise short-circuit (predecessor work on same branch chain)
- `MaterializeFolderRenameService` — sibling rename cascade (async marker-backed, different shape)

## Related

- [[CDI self-invocation bypasses interceptor proxy]]
- [[Snapshot for rollback must live outside retry boundary]]
