---
ai_hash: 65a79062ea55ac7f
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-07
entities:
- luz_docs
- security-class changes
- PUT
- PATCH
- updateSecurityClasses endpoint
- updateFolderMetadata
- cascadeFolderParentChangeIfNeeded
- cascadeFolderRenameIfNeeded
- FolderService
- updatePatchFolderMetadata
- _securityClassCode
- _inheritedSecurityClassCode
- _parentFolderIds
- document sentinels
- _isPublic
- _effectiveSecurityClassCodes
- _folderSecurityClassCodes
- materialized search
- count
- get grants
- get denies
- Folder DELETE
- FolderDeletingService
- documents
- Folder CREATE
- MongoDB
- jsonstore SC_MULTI_STATUS
- old security
- document cascade
- parent-change cascade
- rename cascade
source: materialize code review 2026-06-07
status: seedling
tags:
- luz-docs
- materialize
- security
- cascade
- earchive
title: luz_docs folder security-class changes have 3 entry points but only PUT cascades
type: observation
---

# luz_docs folder security-class changes have 3 entry points but only PUT cascades

A folder's security classes can change through three luz_docs entry points, but as of sprint-156 only one triggers the materialize document cascade:

1. **PUT** `updateFolderMetadata` (FolderService ~335) — calls both `cascadeFolderParentChangeIfNeeded` and `cascadeFolderRenameIfNeeded`. Cascades.
2. **PATCH** `updatePatchFolderMetadata` (~521) — handles `/_securityClassCode`, `/_inheritedSecurityClassCode`, `/_parentFolderIds` ops but only fires the rename cascade. No parent-change cascade.
3. **updateSecurityClasses endpoint** (`/folders/{id}/security-classes`, ~679) — builds a `/_securityClassCode` patch and routes through path 2. No cascade at all.

So PATCH-based security changes leave document sentinels (`_isPublic`, `_effectiveSecurityClassCodes`, `_folderSecurityClassCodes`) stale → materialized search/count/get grants or denies on old security. When adding any new folder-mutation path, wire `cascadeFolderParentChangeIfNeeded` (it diffs pre/post code unions itself — cheap if nothing changed). Folder DELETE is safe (FolderDeletingService re-stamps docs); folder CREATE needs nothing.

## Related

- [[3 Resources/Data/MongoDB/Deterministic Mongo pipeline updates return matched-not-modified; treat jsonstore SC_MULTI_STATUS as benign]]

%% ai-graph-start %%

**Related notes:**
- [[luz_docs bulk folder PATCH runs the materialize cascade once per entry]]
- [[luz_docs parent-change cascade pipeline rebuilds _folderSecurityClassCodes positionally then re-derives the sentinels]]
- [[Put vs Patch UpdatingSecurityClassFolderProcess prefix denotes input shape, not logic]]
- [[Folder recovery reuses the parent-change materialize cascade]]
- [[Folder recovery must recompute inherited security after deletion statuses are cleared]]

**Relations:**
- luz_docs — *HAS_FEATURE* — security-class changes
- security-class changes — *HAS_ENTRY_POINT* — PUT
- security-class changes — *HAS_ENTRY_POINT* — PATCH
- security-class changes — *HAS_ENTRY_POINT* — updateSecurityClasses endpoint
- PUT — *TRIGGERS* — document cascade
- PATCH — *DOES_NOT_TRIGGER* — parent-change cascade
- updateSecurityClasses endpoint — *DOES_NOT_TRIGGER* — document cascade
- updateFolderMetadata — *IS_PART_OF* — FolderService
- updateFolderMetadata — *CALLS* — cascadeFolderParentChangeIfNeeded
- updateFolderMetadata — *CALLS* — cascadeFolderRenameIfNeeded
- updateFolderMetadata — *TRIGGERS* — document cascade
- updatePatchFolderMetadata — *HANDLES* — _securityClassCode
- updatePatchFolderMetadata — *HANDLES* — _inheritedSecurityClassCode
- updatePatchFolderMetadata — *HANDLES* — _parentFolderIds
- updatePatchFolderMetadata — *FIRES* — rename cascade
- updatePatchFolderMetadata — *DOES_NOT_FIRE* — parent-change cascade
- updateSecurityClasses endpoint — *BUILDS* — _securityClassCode
- updateSecurityClasses endpoint — *ROUTES_THROUGH* — updatePatchFolderMetadata
- PATCH — *LEAVES_STALE* — document sentinels
- document sentinels — *INCLUDE* — _isPublic
- document sentinels — *INCLUDE* — _effectiveSecurityClassCodes
- document sentinels — *INCLUDE* — _folderSecurityClassCodes
- PATCH — *CAUSES* — old security
- old security — *AFFECTS* — materialized search
- old security — *AFFECTS* — count
- old security — *AFFECTS* — get grants
- old security — *AFFECTS* — get denies
- Folder DELETE — *IS_SAFE_VIA* — FolderDeletingService
- FolderDeletingService — *RE_STAMPS* — documents
- Folder CREATE — *REQUIRES* — nothing
- luz_docs — *RELATED_TO* — MongoDB
- luz_docs — *RELATED_TO* — jsonstore SC_MULTI_STATUS

%% ai-graph-end %%