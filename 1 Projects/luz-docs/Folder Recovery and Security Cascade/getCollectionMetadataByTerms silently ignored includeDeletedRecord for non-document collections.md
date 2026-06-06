---
title: "getCollectionMetadataByTerms silently ignored includeDeletedRecord for non-document collections"
created: 2026-06-04
type: lesson
status: seedling
source: "session 2026-06-04, JsonStoreMongoService.java"
tags: [luz-docs, gotcha, LUZ-155136, api-design]
---

# getCollectionMetadataByTerms silently ignored includeDeletedRecord for non-document collections

In luz_docs, `JsonStoreMongoService.getCollectionMetadataByTerms(tenantId, collection, condition, isSkipSecurityClasses, isIncludeDeletedRecord)` only applied its two boolean flags when `collection == DOCUMENT_COLLECTION`. For the folder collection the flags were silently dropped, so `SearchRequest`'s default `includeDeletedRecords=false` kicked in and the query always excluded soft-deleted folders — even though callers like `getInheritedParentSecurityClass` and `existFolderIds` explicitly passed `true, true` with clear intent.

**Consequence (LUZ-155136 follow-up):** during folder recovery the inherited-security recompute runs while the subtree is still soft-deleted; the child's parent lookup can't find the still-trashed parent → `FolderNotFoundException` → only the top folder gets a fresh `inheritedSecurityClassCode`, descendants stay stale.

**Lesson:** a boolean parameter that is conditionally honored per collection/type is a trap — callers read the signature and assume it works. Either honor it everywhere or remove it from the signature. Fixed by applying `includeDeletedRecords` for all collections and downgrading `existFolderIds` to pass `false` (preserving its effective 'must be a live folder' validation semantics).

## Related
- [[Put vs Patch UpdatingSecurityClassFolderProcess prefix denotes input shape, not logic]]
