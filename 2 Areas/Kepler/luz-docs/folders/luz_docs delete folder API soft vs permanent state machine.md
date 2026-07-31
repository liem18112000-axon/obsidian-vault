---
title: "luz_docs delete folder API soft vs permanent state machine"
created: 2026-06-05
type: concept
status: seedling
source: "FolderResource.java / FolderValidation.java, session 2026-06-05"
tags: [luz-docs, folders, delete, api]
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
