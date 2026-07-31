---
title: Materialize appendAsPatchOps uses RFC-6902 replace for sentinel fields
created: 2026-06-02
status_fixed: 2026-06-02
type: lesson
status: resolved
source: code review of MaterializeState.appendAsPatchOps + MaterializeCascadeService.stampMaterializeOnPatch
tags:
  - luz-docs
  - materialize
  - rfc-6902
  - backfill
  - gotcha
---

# Materialize appendAsPatchOps uses RFC-6902 replace for sentinel fields

`MaterializeState.appendAsPatchOps` tacks three ops onto the END of every wire patch query produced by `stampMaterializeOnPatch`:

```json
{ "op": "replace", "path": "/_isPublic",                 "value": <bool>  }
{ "op": "replace", "path": "/_effectiveSecurityClassCodes", "value": [...] }
{ "op": "replace", "path": "/_folderNames",              "value": [...]   }
```

All three use **`replace`**. Under strict RFC 6902 semantics, `replace` requires the target path to already exist; applying `replace` to a missing field is an error.

> [!warning] Backfill window risk
> During a materialize rollout, old documents in the tenant **do not yet have** `_isPublic` / `_effectiveSecurityClassCodes` / `_folderNames`. Any PATCH against such a doc routes through `stampMaterializeOnPatch` (because `shouldUseMaterialized(tenantId)` already flipped true) and appends `replace` ops for fields that don't exist. If the storage layer is strict, the patch fails for every unmaterialized doc until the backfill campaign finishes.

## Why it might still work today

`jsonStoreMongoService.updatePatchMetadata` likely translates `replace` into a Mongo `$set`, which is upsert-friendly and does not care whether the field pre-exists. The bug would only manifest if the JSON-Patch pre-check happened **on the application side** before the Mongo update -- which `simulatePatchedDocument` does **in-memory** but only for the input patch (not for the appended ops; the appended ops are emitted **after** simulation, line 88-94 of `MaterializeCascadeService`).

## What to verify

1. Inspect `jsonStoreMongoService.updatePatchMetadata` (in `luz_jsonstore` repo) -- confirm `replace` translates to `$set` and does not assert prior existence.
2. Re-run the materialize backfill rehearsal with a mixed-state tenant and patch a pre-backfill document; observe whether it 4xx's.

## Fix candidate (IMPLEMENTED 2026-06-02)

Switch the three appended ops from `replace` to `add`. Under RFC 6902, `add` is idempotent for object members -- if the field exists it's replaced, if not it's set. Same wire size, strictly safer.

> [!success] Resolved
> `MaterializeState.appendAsPatchOps` now emits `add` ops via `addOp()` helper. Backed by `MaterializeStateTest.appendAsPatchOps_on_empty_patch_returns_three_add_ops` and `MaterializeCascadeServiceTest.stampMaterializeOnPatch_appended_ops_use_add_operator_and_correct_paths`.

```java
private static JsonObject addOp(String path, JsonValue value) {
    return Json.createObjectBuilder()
            .add(Constants.PATCH_OPERATOR, Operation.ADD.getValue())
            .add(Constants.PATCH_PATH, path)
            .add(Constants.PATCH_VALUE, value)
            .build();
}
```

## Related

- [[Materialize bulk PATCH fans out into N serial per-doc PATCH calls]]
- [[securityClassCodes scalar string breaks materialize sentinels]]
- [[Missing folder reference produces fail-closed materialize state]]
- [[flattenArrayAddOps runs only in materialize branch]]
- [[01 Overview - Folder Rename Cascade]]
- [[05 Retry Flow]]
