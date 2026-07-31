---
title: "luz_docs folder security-class changes have 3 entry points but only PUT cascades"
created: 2026-06-07
type: observation
status: seedling
source: "materialize code review 2026-06-07"
tags: [luz-docs, materialize, security, cascade, earchive]
---

# luz_docs folder security-class changes have 3 entry points but only PUT cascades

A folder's security classes can change through three luz_docs entry points, but as of sprint-156 only one triggers the materialize document cascade:

1. **PUT** `updateFolderMetadata` (FolderService ~335) — calls both `cascadeFolderParentChangeIfNeeded` and `cascadeFolderRenameIfNeeded`. Cascades.
2. **PATCH** `updatePatchFolderMetadata` (~521) — handles `/_securityClassCode`, `/_inheritedSecurityClassCode`, `/_parentFolderIds` ops but only fires the rename cascade. No parent-change cascade.
3. **updateSecurityClasses endpoint** (`/folders/{id}/security-classes`, ~679) — builds a `/_securityClassCode` patch and routes through path 2. No cascade at all.

So PATCH-based security changes leave document sentinels (`_isPublic`, `_effectiveSecurityClassCodes`, `_folderSecurityClassCodes`) stale → materialized search/count/get grants or denies on old security. When adding any new folder-mutation path, wire `cascadeFolderParentChangeIfNeeded` (it diffs pre/post code unions itself — cheap if nothing changed). Folder DELETE is safe (FolderDeletingService re-stamps docs); folder CREATE needs nothing.

## Related

- [[3 Resources/Data/MongoDB/Deterministic Mongo pipeline updates return matched-not-modified; treat jsonstore SC_MULTI_STATUS as benign]]
