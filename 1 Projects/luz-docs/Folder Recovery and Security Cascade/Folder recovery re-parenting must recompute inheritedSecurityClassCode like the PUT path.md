---
title: "Folder recovery re-parenting must recompute inheritedSecurityClassCode like the PUT path"
aliases:
  - "Folder recovery re-parent leaves folder-side inheritedSecurityClassCode stale"
created: 2026-06-04
type: lesson
status: budding
source: "LUZ-155107 / LUZ-155136, session 2026-06-04"
tags: [luz-docs, security-class, folder-recovery, inherited-security, bug, gotcha]
---

# Folder recovery re-parenting must recompute inheritedSecurityClassCode like the PUT path

**Bug (pre-existing, independent of materialize):** recovering a folder with re-parenting (`restore.parentFolderIds` in the recovery body) goes through `FolderService.restoreToNewParentFolderIds`, which writes the new `parentFolderIds` via a raw `jsonStoreMongoService.updateFolderMetadata`. It does NOT recompute the folder's own `inheritedSecurityClassCode` and does not cascade to subfolders — so a folder deleted under a restricted parent and recovered under a public one (or vice versa) keeps the old inherited class, silently mis-securing everything beneath it.

**Contrast, the normal PUT path:** `processUpdatingFolderMetadataByPut` runs `PutUpdatingSecurityClassFolderProcess`, which on a `parentFolderIds` change recomputes `inheritedSecurityClassCode` from the new parents via `getInheritedParentSecurityClass` and recursively re-runs itself on every subfolder (`doSubFolders` calls `.run()` directly, bypassing the changed-check).

**Fix (LUZ-155107, refined in LUZ-155136):** reuse `PutUpdatingSecurityClassFolderProcess` after persisting the new `parentFolderIds`, for the folder **and its whole subtree**.

Why it compounds: any *doc-side* materialize recompute reads the folder's current `securityClassCode + inheritedSecurityClassCode`, so a stale folder-side field makes even a "correct" cascade stamp stale effective security onto documents. Fixing the doc-side cascade alone is not enough.

Subtlety: `getSubFolders(..., false)` includes soft-deleted folders, so the process operates on a still-deleted subtree during recovery.

## Related

- [[FolderService.recoverFolder is not materialize-aware]]
- [[Put vs Patch UpdatingSecurityClassFolderProcess prefix denotes input shape, not logic]]
- [[Folder recovery must recompute inherited security after deletion statuses are cleared]]
