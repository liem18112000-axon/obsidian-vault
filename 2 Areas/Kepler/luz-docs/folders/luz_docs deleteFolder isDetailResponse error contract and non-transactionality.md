---
title: "luz_docs deleteFolder isDetailResponse error contract and non-transactionality"
created: 2026-06-05
type: lesson
status: seedling
source: "FolderResource / FolderDeletingService, session 2026-06-05"
tags: [luz-docs, folders, delete, error-handling, gotcha]
---

# luz_docs deleteFolder isDetailResponse error contract and non-transactionality

The luz_docs delete-folder endpoint changes its error contract based on the `is-detail-response` query param (`FolderResource.deleteFolder`):

- `is-detail-response=false` (default): any exception propagates → mapped status (404/400/500), no body detail about partial progress.
- `is-detail-response=true`: exceptions are swallowed and the endpoint returns **500 with the partial `details` body** (`DetailsDeletingBeanParam.getDetails()`) listing what *was* deleted/updated before the failure (deletedFolders, deletedDocuments, mapDocumentRemovedFolder).

Two related gotchas:
1. The deletion pipeline is **not transactional** — docs are updated/deleted one by one, then folders; a mid-recursion failure leaves a partially deleted tree. The detail response is the only visibility into that partial state.
2. `documentStatisticService.fireCacheTenantTokenEvent` runs in `finally` — the statistics refresh fires even when deletion failed.

Also: every member of the subtree (folders *and* documents) is gated by `verifySecurityClasses` against the token's security classes — one inaccessible item anywhere in the tree throws `DocumentMismatchSecurityClassCodeException` and aborts the walk.

## Related

- [[luz_docs delete folder API soft vs permanent state machine]]
- [[luz_docs folder delete shared document handling]]
