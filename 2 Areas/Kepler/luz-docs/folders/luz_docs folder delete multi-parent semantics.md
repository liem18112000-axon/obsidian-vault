---
ai_hash: 171e9e067c3e7809
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-05
entities: []
source: FolderUtil.filterDeleteFolder / FolderValidation, session 2026-06-05
status: seedling
tags:
- luz-docs
- folders
- delete
- multi-parent
title: luz_docs folder delete multi-parent semantics
type: concept
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

%% ai-graph-start %%

**Related notes:**
- [[luz_docs delete folder API soft vs permanent state machine]]
- [[luz_docs folder delete shared document handling]]
- [[Batched folder delete strips folder ids via union updateMany]]
- [[luz_docs deleteFolder isDetailResponse error contract and non-transactionality]]
- [[luz-docs delete detail-response field for removed links is removedLinkParents not removedLink]]

%% ai-graph-end %%