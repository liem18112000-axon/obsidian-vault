---
ai_hash: 1891e6c00628353f
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-04
entities:
- FolderService
- recoverFolder
- '*UpdatingSecurityClassFolderProcess'
- Mockito
- getInheritedParentSecurityClass
- existFolderIds
- jsonStoreMongoService
- getCollectionMetadataByTerms
- filterRecoveryDocument
- NPEs
- FOLDER_COLLECTION
- securityClassCodes
- DOCUMENT_COLLECTION
- folderUtil.getSubFolders
- updatePatchMetadata
- /inheritedSecurityClassCodes
- patch
- op add
- op replace
- buildPatchQuery
- paging loops
- subList.clear()
- '[[Folder recovery re-parenting must recompute inheritedSecurityClassCode like the
  PUT path]]'
- inheritedSecurityClassCode
- PUT path
source: session 2026-06-04
status: seedling
tags:
- luz-docs
- mockito
- unit-testing
- gotcha
title: Unit-testing FolderService recoverFolder requires per-collection stubs because
  process objects call back into the real service
type: howto
---

# Unit-testing FolderService recoverFolder requires per-collection stubs because process objects call back into the real service

In luz_docs, `FolderService` news up its `*UpdatingSecurityClassFolderProcess` objects inline (not injected), passing `this`. So a Mockito test of `FolderService.recoverFolder` runs the **real** process, which calls back into the **real** service (`getInheritedParentSecurityClass`, `existFolderIds`). Both funnel into `jsonStoreMongoService.getCollectionMetadataByTerms(tenant, collection, condition, true, true)`, and `filterRecoveryDocument` hits the same method for the documents collection — a null default stub there NPEs the for-each.

Technique: stub `getCollectionMetadataByTerms` **per collection name** (`eq(FOLDER_COLLECTION)` → parent folders with `securityClassCodes`; `eq(DOCUMENT_COLLECTION)` → empty array), stub `folderUtil.getSubFolders` → empty, and verify the recompute via `updatePatchMetadata` with the `/inheritedSecurityClassCodes` patch (op `add` when the folder had no inherited codes, `replace` otherwise — see `buildPatchQuery`). The paging loops in `existFolderIds`/`getInheritedParentSecurityClass` terminate via `subList.clear()` on the backing list, so a single stubbed page is enough.

See [[Folder recovery re-parenting must recompute inheritedSecurityClassCode like the PUT path]].

## Related

- [[Folder recovery re-parenting must recompute inheritedSecurityClassCode like the PUT path]]

%% ai-graph-start %%

**Related notes:**
- [[Interaction-style mocks hide ordering bugs that a stateful in-memory fake exposes]]
- [[Folder recovery must recompute inherited security after deletion statuses are cleared]]
- [[getCollectionMetadataByTerms silently ignored includeDeletedRecord for non-document collections]]
- [[Folder recovery re-parenting must recompute inheritedSecurityClassCode like the PUT path]]
- [[FolderService.recoverFolder is not materialize-aware]]

**Relations:**
- FolderService — *news up* — *UpdatingSecurityClassFolderProcess
- *UpdatingSecurityClassFolderProcess — *calls back into* — FolderService
- FolderService — *has method* — recoverFolder
- Mockito — *used for testing* — FolderService.recoverFolder
- recoverFolder — *runs* — real process
- real process — *calls* — getInheritedParentSecurityClass
- real process — *calls* — existFolderIds
- getInheritedParentSecurityClass — *funnels into* — jsonStoreMongoService.getCollectionMetadataByTerms
- existFolderIds — *funnels into* — jsonStoreMongoService.getCollectionMetadataByTerms
- filterRecoveryDocument — *hits* — jsonStoreMongoService.getCollectionMetadataByTerms
- jsonStoreMongoService.getCollectionMetadataByTerms — *causes* — NPEs
- jsonStoreMongoService.getCollectionMetadataByTerms — *is stubbed per* — collection name
- stubbing — *for* — FOLDER_COLLECTION
- FOLDER_COLLECTION — *contains* — parent folders with securityClassCodes
- stubbing — *for* — DOCUMENT_COLLECTION
- DOCUMENT_COLLECTION — *returns* — empty array
- folderUtil.getSubFolders — *is stubbed to return* — empty
- updatePatchMetadata — *uses* — /inheritedSecurityClassCodes
- /inheritedSecurityClassCodes — *is a type of* — patch
- patch — *can be* — op add
- patch — *can be* — op replace
- buildPatchQuery — *determines* — op add
- buildPatchQuery — *determines* — op replace
- paging loops — *in* — existFolderIds
- paging loops — *in* — getInheritedParentSecurityClass
- paging loops — *terminate via* — subList.clear()
- FolderService.recoverFolder — *requires* — per-collection stubs
- [[Folder recovery re-parenting must recompute inheritedSecurityClassCode like the PUT path]] — *is related to* — FolderService recoverFolder
- [[Folder recovery re-parenting must recompute inheritedSecurityClassCode like the PUT path]] — *discusses* — inheritedSecurityClassCode
- [[Folder recovery re-parenting must recompute inheritedSecurityClassCode like the PUT path]] — *discusses* — PUT path

%% ai-graph-end %%