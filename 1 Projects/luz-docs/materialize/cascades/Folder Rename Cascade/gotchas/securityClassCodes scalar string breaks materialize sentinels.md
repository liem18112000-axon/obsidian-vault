---
ai_hash: 62df6ad378b71a2d
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-02
entities:
- securityClassCodes
- scalar string
- materialize sentinels
- DocumentServiceValidation.validateAndNormalizeSecurityClassCodes
- simulatePatchedDocument
- JsonPatch.apply
- MaterializeCompute.compute
- getJsonArrayOrEmpty
- _effectiveSecurityClassCodes
- _isPublic
- backend
- JsonString
- luz-skill-update-document-security-class
- single-doc endpoint
- bulk path
- materialize compute step
- empty array
- 400 SECURITY_CLASS_CODE_IS_NOT_CORRECT_FORMAT
- verifyAndNormalizeSecurityClassCodesOp
- verifyAndUpdateFolderIds
- folderIds
- wrap-to-array path
- top-level /securityClassCodes
- indexed paths
- ADD ops
- DocumentServiceValidationTest.validateAndNormalizeSecurityClassCodes_top_level_scalar_{replace,add}_wraps_to_array
- DocumentServiceValidationTest.validateAndNormalizeSecurityClassCodes_add_with_no_value_throws_400
- Materialize bulk PATCH fans out into N serial per-doc PATCH calls
- flattenArrayAddOps runs only in materialize branch
- Missing folder reference produces fail-closed materialize state
- Materialize appendAsPatchOps uses RFC-6902 replace for sentinel fields
- 01 Overview - Folder Rename Cascade
- JSON string scalar
- JSON array
- RFC-6902
- materialize union
- document
- folder
source: code review of DocumentServiceValidation.validateAndNormalizeSecurityClassCodes
  + MaterializeCompute
status: resolved
status_fixed: 2026-06-02
tags:
- luz-docs
- materialize
- security-class-codes
- data-divergence
- gotcha
title: securityClassCodes scalar string breaks materialize sentinels
type: lesson
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

%% ai-graph-start %%

**Related notes:**
- [[RFC-6902 replace at array index expects a scalar element not an array]]
- [[Materialize appendAsPatchOps uses RFC-6902 replace for sentinel fields]]
- [[Missing folder reference produces fail-closed materialize state]]
- [[flattenArrayAddOps runs only in materialize branch]]
- [[scalar add to securityClassCodes tail is a known luz-docs IT pre-existing failure]]

**Relations:**
- scalar string — *breaks* — materialize sentinels
- DocumentServiceValidation.validateAndNormalizeSecurityClassCodes — *accepts* — scalar string
- DocumentServiceValidation.validateAndNormalizeSecurityClassCodes — *processes* — securityClassCodes
- DocumentServiceValidation.validateAndNormalizeSecurityClassCodes — *passes through* — scalar string
- simulatePatchedDocument — *applies op via* — JsonPatch.apply
- JsonPatch.apply — *stores* — securityClassCodes
- securityClassCodes — *as* — JSON string scalar
- MaterializeCompute.compute — *reads field via* — getJsonArrayOrEmpty
- getJsonArrayOrEmpty — *returns* — empty array
- empty array — *for* — non-array value
- securityClassCodes — *is silently dropped from* — materialize union
- _effectiveSecurityClassCodes — *does not include* — securityClassCodes
- _isPublic — *can flip to* — true
- _isPublic — *flips if* — no folder contributes codes
- document — *is missed by* — subsequent reads
- backend — *persists* — securityClassCodes
- securityClassCodes — *as* — JsonString
- luz-skill-update-document-security-class — *documents* — array-append broken
- array-append — *broken on* — single-doc endpoint
- bulk path — *inherits quirk from* — single-doc endpoint
- materialize compute step — *relies on* — JSON array
- fix candidate — *normalize* — scalar string
- scalar string — *into* — JSON array
- normalize — *occurs in* — verifyAndNormalizeSecurityClassCodesOp
- verifyAndNormalizeSecurityClassCodesOp — *mirrors* — verifyAndUpdateFolderIds
- verifyAndUpdateFolderIds — *handles* — folderIds
- fix candidate — *reject* — scalar string
- scalar string — *with* — 400 SECURITY_CLASS_CODE_IS_NOT_CORRECT_FORMAT
- fix — *implemented* — wrap-to-array path
- wrap-to-array path — *applies to* — top-level /securityClassCodes
- indexed paths — *maintain* — scalar string
- ADD ops — *without value field* — throw 400
- fix — *backed by* — DocumentServiceValidationTest.validateAndNormalizeSecurityClassCodes_top_level_scalar_{replace,add}_wraps_to_array
- fix — *backed by* — DocumentServiceValidationTest.validateAndNormalizeSecurityClassCodes_add_with_no_value_throws_400
- securityClassCodes scalar string breaks materialize sentinels — *is related to* — Materialize bulk PATCH fans out into N serial per-doc PATCH calls
- securityClassCodes scalar string breaks materialize sentinels — *is related to* — flattenArrayAddOps runs only in materialize branch
- securityClassCodes scalar string breaks materialize sentinels — *is related to* — Missing folder reference produces fail-closed materialize state
- securityClassCodes scalar string breaks materialize sentinels — *is related to* — Materialize appendAsPatchOps uses RFC-6902 replace for sentinel fields
- securityClassCodes scalar string breaks materialize sentinels — *is related to* — 01 Overview - Folder Rename Cascade
- JSON string scalar — *is not* — JSON array

%% ai-graph-end %%