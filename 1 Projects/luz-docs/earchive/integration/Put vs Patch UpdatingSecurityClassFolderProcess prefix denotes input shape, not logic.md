---
title: "Put vs Patch UpdatingSecurityClassFolderProcess prefix denotes input shape, not logic"
aliases:
  - "Choose Put vs Patch UpdatingSecurityClassFolderProcess by input shape not endpoint verb"
created: 2026-06-04
type: concept
status: seedling
source: "session 2026-06-04, FolderService.java"
tags: [luz-docs, security-class, design-decision, design-rationale, folder-recovery, LUZ-155136]
---

# Put vs Patch UpdatingSecurityClassFolderProcess prefix denotes input shape, not logic

In luz_docs the `Put`/`Patch` prefixes on `UpdatingSecurityClassFolderProcess` subclasses describe the **input shape**, not the calling endpoint and not different recompute logic — both share the same subtree security-class recompute in the parent class.

- **Put variant** — takes two full metadata JsonObjects (before/after) and gates on `isChangingArrayValue(parentFolderIds, ...)`.
- **Patch variant** — takes a JSON-Patch op array and is a thin adapter: finds the `/parentFolderIds` op, re-fetches the folder via `folderService.getFolderById` (extra DB round-trip + security-class visibility check), then delegates to the Put process anyway.

**Rule:** if the call site already holds the full before/after metadata, call Put directly. Folder recovery (LUZ-155136 fix in `FolderService`) does exactly this — recovery semantically *is* a PUT (it replaces the whole `parentFolderIds` value with a known final list), so routing through Patch would mean fabricating fake patch ops plus a pointless re-fetch, only to land in the same Put code. Patch exists solely for the real PATCH endpoint, where the final state is not known up front.

## Related

- [[Folder recovery re-parenting must recompute inheritedSecurityClassCode like the PUT path]]
