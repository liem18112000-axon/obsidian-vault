---
title: RFC-6902 replace at array index expects a scalar element not an array
created: 2026-06-02
type: concept
status: seedling
source: RFC 6902 §4.3 + JSON-Patch behaviour observed in luz-docs verifyAndUpdateFolderIds
tags:
  - json-patch
  - rfc-6902
  - luz-docs
  - concept
aliases:
  - JSON-Patch array index replace semantics
---

# RFC-6902 replace at array index expects a scalar element not an array

For an array-typed target field `foo`, JSON-Patch distinguishes between **whole-field** ops and **indexed/append** ops by path:

| Path        | Op       | Value shape   | Result                                                |
|-------------|----------|---------------|-------------------------------------------------------|
| `/foo`      | replace  | array `[…]`   | replaces the whole field with `[…]`                   |
| `/foo`      | replace  | scalar `"x"`  | replaces the whole field with the scalar (now non-array) |
| `/foo/0`    | replace  | scalar `"x"`  | replaces element at index 0 with `"x"`                |
| `/foo/0`    | replace  | array `[a,b]` | replaces element 0 with the **whole array** → `[[a,b], …]` (nested) |
| `/foo/-`    | add      | scalar `"x"`  | appends `"x"`                                          |
| `/foo/-`    | add      | array `[a,b]` | appends the **whole array** as one element → `[…, [a,b]]` (nested) |

> [!warning] Common pitfall
> Code that normalizes "single id or array of ids" by wrapping a scalar in an array works correctly for the top-level path but **breaks** for indexed/append paths -- it turns `replace /foo/0 "x"` into `replace /foo/0 ["x"]`, producing nested arrays.

## How luz-docs handles this

`DocumentService.verifyAndUpdateFolderIds` branches:
- Top-level `/folderIds` REPLACE → require array value.
- Indexed `/folderIds/<idx>` or `/folderIds/-` with **scalar** string → existence-check, return op unchanged (no wrapping).
- Indexed `/folderIds/<idx>` with array value → would produce nested array, but `JsonObjectUtil.flattenArrayAddOps` (called inside `stampMaterializeOnPatch`) explodes it into N single-value ops before the wire patch is applied.

## Why this matters

Library-level validators that "normalize all values to arrays" silently corrupt data when the path is indexed. The fix is path-shape aware: only wrap when the path targets the whole field.

## Related

- [[flattenArrayAddOps runs only in materialize branch]]
- [[securityClassCodes scalar string breaks materialize sentinels]]
- [[Materialize appendAsPatchOps uses RFC-6902 replace for sentinel fields]]
