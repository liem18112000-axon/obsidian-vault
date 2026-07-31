---
ai_hash: a8fdeb1a9313f565
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-05
entities: []
source: FolderUtil / FolderDeletingService, session 2026-06-05
status: seedling
tags:
- luz-docs
- folders
- documents
- delete
- materialize
title: luz_docs folder delete shared document handling
type: concept
---

# luz_docs folder delete shared document handling

When a luz_docs folder is deleted, each document in it is judged by `FolderUtil.isDeletableDocument`:

- Document referenced by **only this folder** (`folderIds.size() == 1`) → the document is deleted along with the folder (soft or permanent, matching the folder operation).
- **Permanent** folder delete: document dies only if *all* of its `folderIds` are inside the deletion set; otherwise the deleted folder ids are stripped from the document's `folderIds` and the doc survives (`updateDocumentInRemoveFolder`).
- **Soft** folder delete: document is soft-deleted only if *no remaining folder is alive* — `isExistNonSoftDeletedFolder` checks each leftover folder's `deletionStatus`; one live folder elsewhere keeps the document alive (its `folderIds` is trimmed instead).

The `folderIds` trim is also the only place the **materialize cascade** fires during folder deletion (`materializeFacade.shouldCascadeDocument` → `onDocumentChange`) — soft-deleting the folder metadata itself does not re-stamp materialized sentinels.

## Related

- [[luz_docs folder delete multi-parent semantics]]
- [[luz_docs folder deletion is not transactional]]

%% ai-graph-start %%

**Related notes:**
- [[luz_docs folder delete multi-parent semantics]]
- [[Batched folder delete strips folder ids via union updateMany]]
- [[luz_docs delete folder API soft vs permanent state machine]]
- [[luz_docs deleteFolder isDetailResponse error contract and non-transactionality]]
- [[Folder recovery reuses the parent-change materialize cascade]]

%% ai-graph-end %%