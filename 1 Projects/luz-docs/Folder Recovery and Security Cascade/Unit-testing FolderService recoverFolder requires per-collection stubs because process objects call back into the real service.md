---
title: "Unit-testing FolderService recoverFolder requires per-collection stubs because process objects call back into the real service"
created: 2026-06-04
type: howto
status: seedling
source: "session 2026-06-04"
tags: [luz-docs, mockito, unit-testing, gotcha]
---

# Unit-testing FolderService recoverFolder requires per-collection stubs because process objects call back into the real service

In luz_docs, `FolderService` news up its `*UpdatingSecurityClassFolderProcess` objects inline (not injected), passing `this`. So a Mockito test of `FolderService.recoverFolder` runs the **real** process, which calls back into the **real** service (`getInheritedParentSecurityClass`, `existFolderIds`). Both funnel into `jsonStoreMongoService.getCollectionMetadataByTerms(tenant, collection, condition, true, true)`, and `filterRecoveryDocument` hits the same method for the documents collection — a null default stub there NPEs the for-each.

Technique: stub `getCollectionMetadataByTerms` **per collection name** (`eq(FOLDER_COLLECTION)` → parent folders with `securityClassCodes`; `eq(DOCUMENT_COLLECTION)` → empty array), stub `folderUtil.getSubFolders` → empty, and verify the recompute via `updatePatchMetadata` with the `/inheritedSecurityClassCodes` patch (op `add` when the folder had no inherited codes, `replace` otherwise — see `buildPatchQuery`). The paging loops in `existFolderIds`/`getInheritedParentSecurityClass` terminate via `subList.clear()` on the backing list, so a single stubbed page is enough.

See [[Folder recovery re-parenting must recompute inheritedSecurityClassCode like the PUT path]].

## Related

- [[Folder recovery re-parenting must recompute inheritedSecurityClassCode like the PUT path]]
