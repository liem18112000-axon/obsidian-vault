---
ai_hash: 40701ccfda180a97
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-02
entities:
- flattenArrayAddOps
- materialize branch
- JSON-Patch ops
- RFC 6902
- add operation
- array index
- MaterializeCascadeService.stampMaterializeOnPatch
- non-materialize jsonstore-bulk path
- executeUpdatingManyDocumentByPatch
- updateManyDocumentMetadataByPatch
- tenant materialize state
- materialize ON
- materialize OFF
- folderIds
- flat folderIds
- nested folderIds
- MaterializeCompute
- loadFoldersById
- folder names
- bulk-service level
- validateAndNormalizeSecurityClassCodes
- removeDuplicatedFolderIds
- single-doc PATCH endpoint
- nested-array bug
- Materialize bulk PATCH fans out into N serial per-doc PATCH calls
- securityClassCodes scalar string breaks materialize sentinels
- Missing folder reference produces fail-closed materialize state
- Materialize appendAsPatchOps uses RFC-6902 replace for sentinel fields
- 01 Overview - Folder Rename Cascade
source: code review of MaterializeCascadeService.stampMaterializeOnPatch + JsonObjectUtil.flattenArrayAddOps
status: seedling
tags:
- luz-docs
- materialize
- json-patch
- rfc-6902
- gotcha
title: flattenArrayAddOps runs only in materialize branch
type: lesson
---

# flattenArrayAddOps runs only in materialize branch

`JsonObjectUtil.flattenArrayAddOps` rewrites JSON-Patch ops like

```json
{ "op": "add", "path": "/folderIds/-", "value": ["a", "b"] }
```

into N single-element ops

```json
{ "op": "add", "path": "/folderIds/-", "value": "a" }
{ "op": "add", "path": "/folderIds/-", "value": "b" }
```

so that RFC 6902's quirk -- where `add` at an array index inserts the **entire array value as one element**, producing `folderIds = [..., ["a","b"]]` -- doesn't poison the document.

The problem: `flattenArrayAddOps` is invoked **only** inside `MaterializeCascadeService.stampMaterializeOnPatch`. The non-materialize jsonstore-bulk path (the `executeUpdatingManyDocumentByPatch` branch of `updateManyDocumentMetadataByPatch`) never flattens.

> [!bug] Behavior depends on tenant materialize state
> The same request body produces different stored shapes:
>
> | Tenant state | Resulting `folderIds` |
> |---|---|
> | materialize ON  | `[..., "a", "b"]` (flat) |
> | materialize OFF | `[..., ["a","b"]]` (nested) |

## Why it's there

`flattenArrayAddOps` was introduced to keep the materialize compute step honest -- `MaterializeCompute` reads `folderIds` as a flat array of strings, so a nested-array shape would either crash `loadFoldersById` or silently mis-resolve folder names.

## Fix candidate

Hoist `flattenArrayAddOps` to the bulk-service level (alongside `validateAndNormalizeSecurityClassCodes` and `removeDuplicatedFolderIds`), so both branches get the same normalization. Same for the single-doc PATCH endpoint.

## Reproduction shape

```json
{
  "entries": ["<docId1>", "<docId2>"],
  "patchQuery": [
    { "op": "add", "path": "/folderIds/-", "value": ["fA", "fB"] }
  ]
}
```

Inspect `documents.<docId>.folderIds` on a non-materialized tenant -- expect the nested-array bug.

## Related

- [[Materialize bulk PATCH fans out into N serial per-doc PATCH calls]]
- [[securityClassCodes scalar string breaks materialize sentinels]]
- [[Missing folder reference produces fail-closed materialize state]]
- [[Materialize appendAsPatchOps uses RFC-6902 replace for sentinel fields]]
- [[01 Overview - Folder Rename Cascade]]

%% ai-graph-start %%

**Related notes:**
- [[RFC-6902 replace at array index expects a scalar element not an array]]
- [[Materialize appendAsPatchOps uses RFC-6902 replace for sentinel fields]]
- [[JSON-Patch remove-to-replace[] conversion skipped folderIds when another field's remove came first]]
- [[Materialize bulk PATCH fans out into N serial per-doc PATCH calls]]
- [[securityClassCodes scalar string breaks materialize sentinels]]

**Relations:**
- flattenArrayAddOps — *runs only in* — materialize branch
- flattenArrayAddOps — *rewrites* — JSON-Patch ops
- RFC 6902 — *describes behavior of* — add operation
- add operation — *at* — array index
- add operation — *inserts entire array value at* — array index
- add operation — *produces* — nested folderIds
- flattenArrayAddOps — *prevents* — nested folderIds
- flattenArrayAddOps — *is invoked by* — MaterializeCascadeService.stampMaterializeOnPatch
- non-materialize jsonstore-bulk path — *is* — executeUpdatingManyDocumentByPatch
- executeUpdatingManyDocumentByPatch — *is a branch of* — updateManyDocumentMetadataByPatch
- non-materialize jsonstore-bulk path — *does not invoke* — flattenArrayAddOps
- folderIds — *shape depends on* — tenant materialize state
- materialize ON — *produces* — flat folderIds
- materialize OFF — *produces* — nested folderIds
- flattenArrayAddOps — *was introduced for* — MaterializeCompute
- MaterializeCompute — *reads* — folderIds
- MaterializeCompute — *expects* — flat folderIds
- nested folderIds — *would crash* — loadFoldersById
- nested folderIds — *would mis-resolve* — folder names
- flattenArrayAddOps — *should be moved to* — bulk-service level
- bulk-service level — *contains* — validateAndNormalizeSecurityClassCodes
- bulk-service level — *contains* — removeDuplicatedFolderIds
- flattenArrayAddOps — *should apply to* — single-doc PATCH endpoint
- non-materialized tenant — *exhibits* — nested-array bug
- nested-array bug — *affects* — folderIds
- flattenArrayAddOps — *is related to* — Materialize bulk PATCH fans out into N serial per-doc PATCH calls
- flattenArrayAddOps — *is related to* — securityClassCodes scalar string breaks materialize sentinels
- flattenArrayAddOps — *is related to* — Missing folder reference produces fail-closed materialize state
- flattenArrayAddOps — *is related to* — Materialize appendAsPatchOps uses RFC-6902 replace for sentinel fields
- flattenArrayAddOps — *is related to* — 01 Overview - Folder Rename Cascade

%% ai-graph-end %%