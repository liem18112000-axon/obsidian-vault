---
ai_hash: 3ea29e45af2bbcc9
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-15
entities: []
source: session 2026-06-15
status: seedling
tags:
- luz-docs
- integration-test
- gotcha
- recover
title: document_service.recover_document drops an empty folderIds list, so it cannot
  clear folders
type: lesson
---

# document_service.recover_document drops an empty folderIds list, so it cannot clear folders

`core/service/document_service.recover_document` builds its request body as `{"folderIds": folder_ids} if folder_ids else {}` — an empty list is falsy, so it is dropped and no `folderIds` key is sent.

Consequence: the helper can never CLEAR a document's folders on recovery. The Java `DocumentService.recoverDocument` only updates folderIds when the restore body `containsKey(FOLDER_IDS)`; with an empty body the document keeps whatever folders it had when deleted.

Fix for tests that need root recovery (clearing folders): bypass the helper and call `api/client/luz_docs_rest_client.recover_document(...)` directly with an explicit `{"folderIds": []}` body. This makes recoverDocument set folderIds to empty and exercises the materialize drops-folder-data cascade branch.

Related: [[DocumentService.recoverDocument re-stamps materialized fields via the cascade diff]]

## Related

- [[DocumentService.recoverDocument re-stamps materialized fields via the cascade diff]]

%% ai-graph-start %%

**Related notes:**
- [[DocumentService.recoverDocument re-stamps materialized fields via the cascade diff]]
- [[FolderService.recoverFolder is not materialize-aware]]
- [[luz_docs folder delete shared document handling]]
- [[Folder recovery must recompute inherited security after deletion statuses are cleared]]
- [[Folder recovery re-parenting must recompute inheritedSecurityClassCode like the PUT path]]

%% ai-graph-end %%