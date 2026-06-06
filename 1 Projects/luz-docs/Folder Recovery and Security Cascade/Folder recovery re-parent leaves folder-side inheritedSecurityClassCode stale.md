---
title: "Folder recovery re-parent leaves folder-side inheritedSecurityClassCode stale"
created: 2026-06-04
type: observation
status: budding
source: "LUZ-155107 investigation, session 2026-06-04"
tags: [luz-docs, folder-recovery, security, inherited-security, bug]
---

# Folder recovery re-parent leaves folder-side inheritedSecurityClassCode stale

Pre-existing bug in luz_docs, independent of materialize: when a folder is recovered with re-parenting (`restore.parentFolderIds` in the recovery body), `FolderService.restoreToNewParentFolderIds` writes the new `parentFolderIds` via a raw `jsonStoreMongoService.updateFolderMetadata` — it does NOT recompute the folder's own `inheritedSecurityClassCode`, and does not cascade to subfolders.

Contrast with the normal PUT path: `processUpdatingFolderMetadataByPut` runs `PutUpdatingSecurityClassFolderProcess`, which (when parentFolderIds changed) recomputes `inheritedSecurityClassCode` from the new parents via `getInheritedParentSecurityClass` and recursively re-runs itself on every subfolder (`doSubFolders` calls `.run()` directly, bypassing the changed-check).

Why it compounds: any *doc-side* materialize recompute reads the folder's current `securityClassCode + inheritedSecurityClassCode` — so if the folder-side field is stale, a 'correct' cascade still stamps stale effective security onto documents. Fixing the doc-side cascade alone is not enough; recovery-with-reparent must run the same PutUpdatingSecurityClassFolderProcess as the PUT path.

Note the subtlety: `getSubFolders(..., false)` includes soft-deleted folders, so the process works on a still-deleted subtree during recovery.

Related: [[FolderService.recoverFolder is not materialize-aware]]

## Related

- [[FolderService.recoverFolder is not materialize-aware]]
