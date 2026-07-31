---
title: "document_service.recover_document drops an empty folderIds list, so it cannot clear folders"
created: 2026-06-15
type: lesson
status: seedling
source: "session 2026-06-15"
tags: [luz-docs, integration-test, gotcha, recover]
---

# document_service.recover_document drops an empty folderIds list, so it cannot clear folders

`core/service/document_service.recover_document` builds its request body as `{"folderIds": folder_ids} if folder_ids else {}` — an empty list is falsy, so it is dropped and no `folderIds` key is sent.

Consequence: the helper can never CLEAR a document's folders on recovery. The Java `DocumentService.recoverDocument` only updates folderIds when the restore body `containsKey(FOLDER_IDS)`; with an empty body the document keeps whatever folders it had when deleted.

Fix for tests that need root recovery (clearing folders): bypass the helper and call `api/client/luz_docs_rest_client.recover_document(...)` directly with an explicit `{"folderIds": []}` body. This makes recoverDocument set folderIds to empty and exercises the materialize drops-folder-data cascade branch.

Related: [[DocumentService.recoverDocument re-stamps materialized fields via the cascade diff]]

## Related

- [[DocumentService.recoverDocument re-stamps materialized fields via the cascade diff]]
