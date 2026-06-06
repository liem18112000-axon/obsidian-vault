---
title: securityClassCodes scalar string breaks materialize sentinels
created: 2026-06-02
status_fixed: 2026-06-02
type: lesson
status: resolved
source: code review of DocumentServiceValidation.validateAndNormalizeSecurityClassCodes + MaterializeCompute
tags:
  - luz-docs
  - materialize
  - security-class-codes
  - data-divergence
  - gotcha
---

# securityClassCodes scalar string breaks materialize sentinels

`DocumentServiceValidation.validateAndNormalizeSecurityClassCodes` accepts a scalar **string** value for `add`/`replace` on `/securityClassCodes` and passes it through unchanged. The op is then applied by `simulatePatchedDocument` via `JsonPatch.apply`, which stores `securityClassCodes` as a JSON string scalar (not an array) in the simulated document.

`MaterializeCompute.compute` then reads the field via `getJsonArrayOrEmpty(simulateMetadata, SECURITY_CLASSCODE)`. That helper returns an empty array for any non-array value, so the just-written code is **silently dropped** from the materialize union.

> [!bug] Net effect
> - `_effectiveSecurityClassCodes` does not include the new code.
> - `_isPublic` can flip to `true` if `docOwnCodes` is now considered empty and no folder contributes codes.
> - Subsequent reads filtered by security class will miss this document even though `securityClassCodes` holds the value.

## Root cause

The backend persists `securityClassCodes` as a `JsonString` scalar (not an array) historically. The skill `luz-skill-update-document-security-class` already documents that array-append (`/securityClassCodes/-`) is broken on the single-doc endpoint for this same reason. The bulk path inherits the quirk plus a second layer: the materialize compute step relies on the field being an array.

## Reproduction shape

```json
{
  "op": "replace",
  "path": "/securityClassCodes",
  "value": "S1"
}
```

After patch: `securityClassCodes: "S1"`, `_effectiveSecurityClassCodes: []`, `_isPublic: true` (if no codes inherited from folders).

## Fix candidates

- Normalize scalar string into a single-element array inside `verifyAndNormalizeSecurityClassCodesOp` (mirrors what `verifyAndUpdateFolderIds` already does for folderIds).
- Reject scalar string with `400 SECURITY_CLASS_CODE_IS_NOT_CORRECT_FORMAT` and force callers to send arrays.

> [!success] Resolved 2026-06-02
> Took the wrap-to-array path, but **only** for top-level `/securityClassCodes`. Indexed paths (`/securityClassCodes/0`, `/securityClassCodes/-`) keep the scalar so JSON-Patch indexed semantics work. ADD ops without a `value` field now throw 400 explicitly instead of falling through. Backed by `DocumentServiceValidationTest.validateAndNormalizeSecurityClassCodes_top_level_scalar_{replace,add}_wraps_to_array` and `..._add_with_no_value_throws_400`.

## Related

- [[Materialize bulk PATCH fans out into N serial per-doc PATCH calls]]
- [[flattenArrayAddOps runs only in materialize branch]]
- [[Missing folder reference produces fail-closed materialize state]]
- [[Materialize appendAsPatchOps uses RFC-6902 replace for sentinel fields]]
- [[01 Overview - Folder Rename Cascade]]
