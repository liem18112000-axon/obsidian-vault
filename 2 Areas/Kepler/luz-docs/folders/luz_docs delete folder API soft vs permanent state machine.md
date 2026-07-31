---
ai_hash: b77c744d2b2c2783
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-05
entities: []
source: FolderResource.java / FolderValidation.java, session 2026-06-05
status: seedling
tags:
- luz-docs
- folders
- delete
- api
title: luz_docs delete folder API soft vs permanent state machine
type: concept
---

# luz_docs delete folder API soft vs permanent state machine

DELETE `/{tenantId}/folders/{folder-id}` in luz_docs is two different operations gated by the `delete-permanent` query param, and each has an opposite precondition enforced by `FolderValidation.validateIsFolderDeleted`:

- **Permanent delete** (`delete-permanent=true`) requires the folder to *already be soft-deleted* (`deletionStatus=true`), otherwise 400 `FOLDER_IS_NOT_DELETED`. You cannot hard-delete a live folder directly.
- **Soft delete** (default) requires the folder to *not already be deleted* (`deletionStatus` must not be `true`), otherwise 400 `FOLDER_IS_DELETED`. You cannot re-trash a trashed folder.

So the lifecycle is strictly: live → soft-deleted (trash) → permanently deleted, with recovery (`POST /{folder-id}/recovery`) as the only path back. Routing happens in `FolderResource.deleteFolder` → `FolderService.deleteFolder` (permanent) vs `FolderService.updateDeletionStatusFolder` (soft).

Gotcha: a folder created before `deletionStatus` existed (no field at all) passes both checks — the validation only fires when the key is present.

## Related

- [[luz_docs folder delete multi-parent semantics]]
- [[luz_docs folder delete shared document handling]]

%% ai-graph-start %%

**Related notes:**
- [[luz_docs folder delete multi-parent semantics]]
- [[luz_docs folder delete shared document handling]]
- [[luz_docs deleteFolder isDetailResponse error contract and non-transactionality]]
- [[Luz delete-folder tests can only delete public folders, not ones carrying a security class]]
- [[Folder recovery reuses the parent-change materialize cascade]]

%% ai-graph-end %%