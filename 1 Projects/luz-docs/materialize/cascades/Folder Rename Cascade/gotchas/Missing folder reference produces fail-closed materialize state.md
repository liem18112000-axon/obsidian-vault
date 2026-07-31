---
title: Missing folder reference produces fail-closed materialize state
created: 2026-06-02
type: lesson
status: seedling
source: code review of MaterializeCompute.compute lines 34-47
tags:
  - luz-docs
  - materialize
  - folder-cascade
  - visibility
  - gotcha
---

# Missing folder reference produces fail-closed materialize state

When `MaterializeCompute.compute` iterates `foldersById` and a folder entry resolves to `null` (folder was deleted but document still references its id), the `Optional.ofNullable(folder).ifPresentOrElse(...)` `else` branch:

- **appends an empty string `""`** to `_folderNames` (as a positional placeholder), but
- does **not** touch `anyPublicFolder`.

Combined with the `_isPublic` formula:

```
_isPublic = docOwnCodes.isEmpty() AND (anyPublicFolder OR foldersById.isEmpty())
```

A document whose only folder references are deleted ends up with:
- `foldersById` non-empty (the entries exist, they just map to `null`)
- `anyPublicFolder = false`
- `_effectiveSecurityClassCodes = []` (no folder contributed codes)
- `_isPublic = false` (if `docOwnCodes` is empty)

> [!warning] Net effect
> The document is **invisible to both public queries and code-filtered queries**. It can only be reached by direct id lookup or by an admin scan.

## Why this matters

Fail-closed is defensible (we'd rather hide than over-share). But it's silent — there is no log line and no cascade marker. The condition is also undetectable from materialize stats alone (`_isPublic=false` + empty `_effectiveSecurityClassCodes` is a normal restricted-no-codes shape; cf. `luz-skill-materialize-stats`).

## Possible mitigations

- Emit a `WARNING` log in `MaterializeCompute` when any folder is `null`.
- Add a cascade marker so the partial-cascade worker can re-evaluate.
- Treat all-folders-missing as `_isPublic = true` when `docOwnCodes` is empty (matches the empty-folderIds branch).
- Compensate at write time: on folder delete, scrub the id from referencing docs' `folderIds`.

## Related

- [[Materialize bulk PATCH fans out into N serial per-doc PATCH calls]]
- [[securityClassCodes scalar string breaks materialize sentinels]]
- [[flattenArrayAddOps runs only in materialize branch]]
- [[Materialize appendAsPatchOps uses RFC-6902 replace for sentinel fields]]
- [[01 Overview - Folder Rename Cascade]]
- [[04 Marker State Machine]]
