---
title: flattenArrayAddOps runs only in materialize branch
created: 2026-06-02
type: lesson
status: seedling
source: code review of MaterializeCascadeService.stampMaterializeOnPatch + JsonObjectUtil.flattenArrayAddOps
tags:
  - luz-docs
  - materialize
  - json-patch
  - rfc-6902
  - gotcha
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
