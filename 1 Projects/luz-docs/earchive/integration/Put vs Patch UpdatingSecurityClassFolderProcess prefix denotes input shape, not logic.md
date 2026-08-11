---
ai_hash: c1c8fabd79628f59
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
aliases:
- Choose Put vs Patch UpdatingSecurityClassFolderProcess by input shape not endpoint
  verb
created: 2026-06-04
entities:
- Put
- Patch
- UpdatingSecurityClassFolderProcess
- input shape
- calling endpoint
- recompute logic
- subtree security-class recompute
- parent class
- metadata JsonObjects
- isChangingArrayValue
- parentFolderIds
- JSON-Patch op array
- adapter
- folderService.getFolderById
- Folder recovery
- LUZ-155136
- FolderService
- PATCH endpoint
- inheritedSecurityClassCode
- PUT path
source: session 2026-06-04, FolderService.java
status: seedling
tags:
- luz-docs
- security-class
- design-decision
- design-rationale
- folder-recovery
- LUZ-155136
title: Put vs Patch UpdatingSecurityClassFolderProcess prefix denotes input shape,
  not logic
type: concept
---

# Put vs Patch UpdatingSecurityClassFolderProcess prefix denotes input shape, not logic

In luz_docs the `Put`/`Patch` prefixes on `UpdatingSecurityClassFolderProcess` subclasses describe the **input shape**, not the calling endpoint and not different recompute logic — both share the same subtree security-class recompute in the parent class.

- **Put variant** — takes two full metadata JsonObjects (before/after) and gates on `isChangingArrayValue(parentFolderIds, ...)`.
- **Patch variant** — takes a JSON-Patch op array and is a thin adapter: finds the `/parentFolderIds` op, re-fetches the folder via `folderService.getFolderById` (extra DB round-trip + security-class visibility check), then delegates to the Put process anyway.

**Rule:** if the call site already holds the full before/after metadata, call Put directly. Folder recovery (LUZ-155136 fix in `FolderService`) does exactly this — recovery semantically *is* a PUT (it replaces the whole `parentFolderIds` value with a known final list), so routing through Patch would mean fabricating fake patch ops plus a pointless re-fetch, only to land in the same Put code. Patch exists solely for the real PATCH endpoint, where the final state is not known up front.

## Related

- [[Folder recovery re-parenting must recompute inheritedSecurityClassCode like the PUT path]]

%% ai-graph-start %%

**Related notes:**
- [[luz_docs folder security-class changes have 3 entry points but only PUT cascades]]
- [[Folder recovery re-parenting must recompute inheritedSecurityClassCode like the PUT path]]
- [[Folder recovery must recompute inherited security after deletion statuses are cleared]]
- [[luz_docs bulk folder PATCH runs the materialize cascade once per entry]]
- [[Folder recovery reuses the parent-change materialize cascade]]

**Relations:**
- Put — *is_prefix_on* — UpdatingSecurityClassFolderProcess
- Patch — *is_prefix_on* — UpdatingSecurityClassFolderProcess
- Put — *describes* — input shape
- Patch — *describes* — input shape
- Put — *does_not_describe* — calling endpoint
- Patch — *does_not_describe* — calling endpoint
- Put — *does_not_describe* — recompute logic
- Patch — *does_not_describe* — recompute logic
- UpdatingSecurityClassFolderProcess — *shares* — subtree security-class recompute
- subtree security-class recompute — *is_in* — parent class
- Put — *takes* — metadata JsonObjects
- Put — *gates_on* — isChangingArrayValue
- isChangingArrayValue — *uses* — parentFolderIds
- Patch — *takes* — JSON-Patch op array
- Patch — *is_a* — adapter
- Patch — *re-fetches_via* — folderService.getFolderById
- Patch — *delegates_to* — Put
- Folder recovery — *is_a* — PUT
- Folder recovery — *is_a_fix_for* — LUZ-155136
- Folder recovery — *is_in* — FolderService
- Patch — *exists_for* — PATCH endpoint
- Folder recovery — *must_recompute* — inheritedSecurityClassCode
- Folder recovery — *is_like* — PUT path

%% ai-graph-end %%