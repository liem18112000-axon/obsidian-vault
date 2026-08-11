---
ai_hash: 76da5f0827adf054
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
aliases:
- Folder recovery re-parent leaves folder-side inheritedSecurityClassCode stale
created: 2026-06-04
entities:
- Folder recovery
- re-parenting
- inheritedSecurityClassCode
- PUT path
- FolderService.restoreToNewParentFolderIds
- jsonStoreMongoService.updateFolderMetadata
- processUpdatingFolderMetadataByPut
- PutUpdatingSecurityClassFolderProcess
- getInheritedParentSecurityClass
- doSubFolders
- LUZ-155107
- LUZ-155136
- doc-side materialize recompute
- securityClassCode
- getSubFolders
- FolderService.recoverFolder
- materialize-aware
- Put vs Patch UpdatingSecurityClassFolderProcess
- deletion statuses
- subfolders
- folder
- subtree
- soft-deleted folders
- documents
- stale effective security
- input shape
- logic
- parentFolderIds
source: LUZ-155107 / LUZ-155136, session 2026-06-04
status: budding
tags:
- luz-docs
- security-class
- folder-recovery
- inherited-security
- bug
- gotcha
title: Folder recovery re-parenting must recompute inheritedSecurityClassCode like
  the PUT path
type: lesson
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

%% ai-graph-start %%

**Related notes:**
- [[Folder recovery must recompute inherited security after deletion statuses are cleared]]
- [[FolderService.recoverFolder is not materialize-aware]]
- [[getCollectionMetadataByTerms silently ignored includeDeletedRecord for non-document collections]]
- [[Folder recovery reuses the parent-change materialize cascade]]
- [[luz_docs folder security-class changes have 3 entry points but only PUT cascades]]

**Relations:**
- Folder recovery — *with re-parenting uses* — FolderService.restoreToNewParentFolderIds
- Folder recovery re-parenting — *requires recomputation of inheritedSecurityClassCode like* — PUT path
- FolderService.restoreToNewParentFolderIds — *updates* — parentFolderIds
- FolderService.restoreToNewParentFolderIds — *uses* — jsonStoreMongoService.updateFolderMetadata
- FolderService.restoreToNewParentFolderIds — *does not recompute* — inheritedSecurityClassCode
- PUT path — *uses* — processUpdatingFolderMetadataByPut
- processUpdatingFolderMetadataByPut — *runs* — PutUpdatingSecurityClassFolderProcess
- PutUpdatingSecurityClassFolderProcess — *recomputes* — inheritedSecurityClassCode
- PutUpdatingSecurityClassFolderProcess — *uses* — getInheritedParentSecurityClass
- PutUpdatingSecurityClassFolderProcess — *recursively processes* — subfolders
- PutUpdatingSecurityClassFolderProcess — *via* — doSubFolders
- LUZ-155107 — *is refined by* — LUZ-155136
- LUZ-155107 — *proposes reuse of* — PutUpdatingSecurityClassFolderProcess
- PutUpdatingSecurityClassFolderProcess — *applies to* — folder
- PutUpdatingSecurityClassFolderProcess — *applies to* — subtree
- doc-side materialize recompute — *reads* — securityClassCode
- doc-side materialize recompute — *reads* — inheritedSecurityClassCode
- stale folder-side field — *causes* — stale effective security
- stale effective security — *on* — documents
- getSubFolders — *includes* — soft-deleted folders
- FolderService.recoverFolder — *is not* — materialize-aware
- Put vs Patch UpdatingSecurityClassFolderProcess — *denotes* — input shape
- Put vs Patch UpdatingSecurityClassFolderProcess — *denotes* — logic
- Folder recovery — *must recompute* — inheritedSecurityClassCode
- inheritedSecurityClassCode — *after* — deletion statuses are cleared

%% ai-graph-end %%