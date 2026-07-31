---
title: "luz_docs folder delete shared document handling"
created: 2026-06-05
type: concept
status: seedling
source: "FolderUtil / FolderDeletingService, session 2026-06-05"
tags: [luz-docs, folders, documents, delete, materialize]
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
