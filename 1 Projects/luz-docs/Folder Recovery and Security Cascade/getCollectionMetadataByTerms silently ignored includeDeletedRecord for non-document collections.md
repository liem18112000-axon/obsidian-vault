---
ai_hash: 343779c10f35eed7
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-04
entities:
- getCollectionMetadataByTerms
- includeDeletedRecord
- luz_docs
- JsonStoreMongoService
- DOCUMENT_COLLECTION
- folder collection
- SearchRequest
- getInheritedParentSecurityClass
- existFolderIds
- FolderNotFoundException
- inheritedSecurityClassCode
- LUZ-155136
- boolean parameter
- Put vs Patch UpdatingSecurityClassFolderProcess prefix denotes input shape, not
  logic
- soft-deleted folders
- descendants
- top folder
source: session 2026-06-04, JsonStoreMongoService.java
status: seedling
tags:
- luz-docs
- gotcha
- LUZ-155136
- api-design
title: getCollectionMetadataByTerms silently ignored includeDeletedRecord for non-document
  collections
type: lesson
---

# getCollectionMetadataByTerms silently ignored includeDeletedRecord for non-document collections

In luz_docs, `JsonStoreMongoService.getCollectionMetadataByTerms(tenantId, collection, condition, isSkipSecurityClasses, isIncludeDeletedRecord)` only applied its two boolean flags when `collection == DOCUMENT_COLLECTION`. For the folder collection the flags were silently dropped, so `SearchRequest`'s default `includeDeletedRecords=false` kicked in and the query always excluded soft-deleted folders — even though callers like `getInheritedParentSecurityClass` and `existFolderIds` explicitly passed `true, true` with clear intent.

**Consequence (LUZ-155136 follow-up):** during folder recovery the inherited-security recompute runs while the subtree is still soft-deleted; the child's parent lookup can't find the still-trashed parent → `FolderNotFoundException` → only the top folder gets a fresh `inheritedSecurityClassCode`, descendants stay stale.

**Lesson:** a boolean parameter that is conditionally honored per collection/type is a trap — callers read the signature and assume it works. Either honor it everywhere or remove it from the signature. Fixed by applying `includeDeletedRecords` for all collections and downgrading `existFolderIds` to pass `false` (preserving its effective 'must be a live folder' validation semantics).

## Related
- [[Put vs Patch UpdatingSecurityClassFolderProcess prefix denotes input shape, not logic]]

%% ai-graph-start %%

**Related notes:**
- [[Folder recovery must recompute inherited security after deletion statuses are cleared]]
- [[Folder recovery re-parenting must recompute inheritedSecurityClassCode like the PUT path]]
- [[FolderService.recoverFolder is not materialize-aware]]
- [[luz-docs folder delete verifies document security classes with one limit-1 Mongo query per folder]]
- [[luz_docs folder security-class changes have 3 entry points but only PUT cascades]]

**Relations:**
- getCollectionMetadataByTerms — *is part of* — luz_docs
- getCollectionMetadataByTerms — *is a method of* — JsonStoreMongoService
- getCollectionMetadataByTerms — *ignored* — includeDeletedRecord
- getCollectionMetadataByTerms — *ignored for* — folder collection
- getCollectionMetadataByTerms — *applied flags only for* — DOCUMENT_COLLECTION
- SearchRequest — *has default for includeDeletedRecords as* — false
- SearchRequest — *caused exclusion of* — soft-deleted folders
- getInheritedParentSecurityClass — *called* — getCollectionMetadataByTerms
- existFolderIds — *called* — getCollectionMetadataByTerms
- getInheritedParentSecurityClass — *intended to include* — deleted records
- existFolderIds — *intended to include* — deleted records
- LUZ-155136 — *is related to* — FolderNotFoundException
- FolderNotFoundException — *caused* — inheritedSecurityClassCode to be stale for descendants
- FolderNotFoundException — *caused* — top folder to get fresh inheritedSecurityClassCode
- boolean parameter — *can be a* — trap
- issue — *fixed by applying* — includeDeletedRecord for all collections
- issue — *fixed by downgrading* — existFolderIds to pass false
- existFolderIds — *validates* — live folder
- this note — *is related to* — Put vs Patch UpdatingSecurityClassFolderProcess prefix denotes input shape, not logic

%% ai-graph-end %%