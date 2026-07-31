---
ai_hash: e81f4c43508643ba
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-05
entities:
- luz_docs deleteFolder
- delete-folder endpoint
- error contract
- is-detail-response
- FolderResource.deleteFolder
- exceptions
- mapped status
- partial details body
- DetailsDeletingBeanParam.getDetails()
- deletedFolders
- deletedDocuments
- mapDocumentRemovedFolder
- deletion pipeline
- transactionality
- docs
- folders
- partially deleted tree
- documentStatisticService.fireCacheTenantTokenEvent
- statistics refresh
- subtree
- verifySecurityClasses
- token's security classes
- DocumentMismatchSecurityClassCodeException
- luz_docs delete folder API soft vs permanent state machine
- luz_docs folder delete shared document handling
source: FolderResource / FolderDeletingService, session 2026-06-05
status: seedling
tags:
- luz-docs
- folders
- delete
- error-handling
- gotcha
title: luz_docs deleteFolder isDetailResponse error contract and non-transactionality
type: lesson
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

%% ai-graph-start %%

**Related notes:**
- [[luz_docs delete folder API soft vs permanent state machine]]
- [[luz_docs folder delete shared document handling]]
- [[luz_docs FolderDeletingServiceIT coverage gaps]]
- [[luz_docs folder delete multi-parent semantics]]
- [[Luz delete-folder tests can only delete public folders, not ones carrying a security class]]

**Relations:**
- luz_docs deleteFolder — *is an* — endpoint
- delete-folder endpoint — *changes* — error contract
- error contract — *depends on* — is-detail-response
- FolderResource.deleteFolder — *implements* — luz_docs deleteFolder
- is-detail-response — *is parameter of* — FolderResource.deleteFolder
- is-detail-response=false — *results in* — exceptions propagating
- exceptions propagating — *lead to* — mapped status
- mapped status — *includes* — 404
- mapped status — *includes* — 400
- mapped status — *includes* — 500
- is-detail-response=true — *results in* — exceptions swallowed
- exceptions swallowed — *lead to* — returns 500
- returns 500 — *with* — partial details body
- partial details body — *from* — DetailsDeletingBeanParam.getDetails()
- partial details body — *lists* — deletedFolders
- partial details body — *lists* — deletedDocuments
- partial details body — *lists* — mapDocumentRemovedFolder
- deletion pipeline — *lacks* — transactionality
- deletion pipeline — *updates* — docs
- deletion pipeline — *updates* — folders
- mid-recursion failure — *creates* — partially deleted tree
- partial details body — *provides visibility into* — partially deleted tree
- documentStatisticService.fireCacheTenantTokenEvent — *runs in* — finally
- statistics refresh — *occurs even if* — deletion failed
- subtree — *members gated by* — verifySecurityClasses
- verifySecurityClasses — *uses* — token's security classes
- inaccessible item — *throws* — DocumentMismatchSecurityClassCodeException
- DocumentMismatchSecurityClassCodeException — *aborts* — walk
- luz_docs deleteFolder — *related to* — luz_docs delete folder API soft vs permanent state machine
- luz_docs deleteFolder — *related to* — luz_docs folder delete shared document handling

%% ai-graph-end %%