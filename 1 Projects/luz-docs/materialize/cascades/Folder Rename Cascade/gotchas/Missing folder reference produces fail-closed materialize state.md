---
ai_hash: fc0d5036a269538f
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-02
entities:
- MaterializeCompute.compute
- foldersById
- folder entry
- 'null'
- _folderNames
- empty string ""
- anyPublicFolder
- _isPublic
- docOwnCodes
- document
- public queries
- code-filtered queries
- direct id lookup
- admin scan
- fail-closed
- log line
- cascade marker
- materialize stats
- _effectiveSecurityClassCodes
- WARNING log
- partial-cascade worker
- folder delete
- folderIds
- Missing folder reference
- materialize state
- Materialize bulk PATCH fans out into N serial per-doc PATCH calls
- securityClassCodes scalar string breaks materialize sentinels
- flattenArrayAddOps runs only in materialize branch
- Materialize appendAsPatchOps uses RFC-6902 replace for sentinel fields
- 01 Overview - Folder Rename Cascade
- 04 Marker State Machine
source: code review of MaterializeCompute.compute lines 34-47
status: seedling
tags:
- luz-docs
- materialize
- folder-cascade
- visibility
- gotcha
title: Missing folder reference produces fail-closed materialize state
type: lesson
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

%% ai-graph-start %%

**Related notes:**
- [[Empty per-folder codes means public, not no-access]]
- [[securityClassCodes scalar string breaks materialize sentinels]]
- [[Materialize appendAsPatchOps uses RFC-6902 replace for sentinel fields]]
- [[Materialize bulk PATCH fans out into N serial per-doc PATCH calls]]
- [[Folder recovery must recompute inherited security after deletion statuses are cleared]]

**Relations:**
- MaterializeCompute.compute — *iterates* — foldersById
- folder entry — *resolves to* — null
- Missing folder reference — *causes* — fail-closed
- Missing folder reference — *affects* — materialize state
- null — *leads to* — _folderNames
- _folderNames — *receives* — empty string ""
- null — *does not update* — anyPublicFolder
- _isPublic — *calculated from* — docOwnCodes
- _isPublic — *calculated from* — anyPublicFolder
- _isPublic — *calculated from* — foldersById
- document — *has* — foldersById
- document — *has* — anyPublicFolder
- document — *has* — _effectiveSecurityClassCodes
- document — *has* — _isPublic
- document — *is invisible to* — public queries
- document — *is invisible to* — code-filtered queries
- document — *is reachable by* — direct id lookup
- document — *is reachable by* — admin scan
- fail-closed — *is* — silent
- silent — *implies no* — log line
- silent — *implies no* — cascade marker
- materialize stats — *cannot detect* — fail-closed
- WARNING log — *is a mitigation* — fail-closed
- cascade marker — *is a mitigation* — fail-closed
- partial-cascade worker — *uses* — cascade marker
- Treat all-folders-missing as _isPublic = true — *is a mitigation* — fail-closed
- Compensate at write time — *is a mitigation* — fail-closed
- folder delete — *triggers* — Compensate at write time
- Compensate at write time — *scrubs* — folderIds
- Missing folder reference — *is related to* — Materialize bulk PATCH fans out into N serial per-doc PATCH calls
- Missing folder reference — *is related to* — securityClassCodes scalar string breaks materialize sentinels
- Missing folder reference — *is related to* — flattenArrayAddOps runs only in materialize branch
- Missing folder reference — *is related to* — Materialize appendAsPatchOps uses RFC-6902 replace for sentinel fields
- Missing folder reference — *is related to* — 01 Overview - Folder Rename Cascade
- Missing folder reference — *is related to* — 04 Marker State Machine

%% ai-graph-end %%