---
title: "luz_docs folder delete multi-parent semantics"
created: 2026-06-05
type: concept
status: seedling
source: "FolderUtil.filterDeleteFolder / FolderValidation, session 2026-06-05"
tags: [luz-docs, folders, delete, multi-parent]
---

# luz_docs folder delete multi-parent semantics

A luz_docs folder can have multiple parents (`parentFolderIds` is an array), and the DELETE folder API treats deletion as *link removal* unless every parent link is covered:

- If the folder has **≥ 2 parents** and no `parent-folder-id` query param is given → 400 `PARENT_FOLDER_ID_IS_REQUIRED` (`validateParentFolderForDeletion`). The API refuses to guess which link you mean.
- If `parent-folder-id` is given but is **not actually one of the folder's parents** → 400 `PARENT_FOLDER_ID_IS_NOT_FOUND`.
- During recursion, `FolderUtil.filterDeleteFolder` only marks a folder *deletable* when its `parentFolderIds` are entirely contained in the set of parents being deleted (`isDeletableFolder`). Otherwise the folder survives and just gets the deleted parent removed from its `parentFolderIds` (`mapFolderNeedRemoveParent` → `updateFolderHavingParentIsDeletedFolder`).

Net effect: deleting one branch of a multi-parented subfolder unlinks it from that branch; the folder itself is only deleted when its *last* parent goes.

## Related

- [[luz_docs delete folder API soft vs permanent state machine]]
- [[luz_docs folder delete shared document handling]]
