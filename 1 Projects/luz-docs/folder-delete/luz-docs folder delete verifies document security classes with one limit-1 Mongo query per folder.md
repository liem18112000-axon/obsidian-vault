---
ai_hash: c17c77f5eefdbb9e
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-10
entities:
- luz-docs folder delete
- document security classes
- Mongo query
- folder
- Java
- verifySecurityClasses
- jsonStoreMongoService.countCollections
- $match
- $count
- result.isEmpty()
- securityClassCodes
- inheritedSecurityClassCodes
- tenant's classes
- DocumentMismatchSecurityClassCodeException
- CollectionUtil.isMatchingSecurityClasses
- _deletionStatus
- getAllDocumentsMetadata
- buildDeletionStatusCondition
- luz-docs folder delete filter double-fetched every subfolder
- Split bulk scans on folderIds.1 exists to separate single-array-element fast path
- document
- violating document
- find filter
- old Java semantics
- empty tenant list
- combined security classes
- whole delete
source: session 2026-06-10, FolderUtil.verifyFolderDocumentsSecurityClasses
status: seedling
tags:
- luz-docs
- mongodb
- security-classes
- performance
title: luz-docs folder delete verifies document security classes with one limit-1
  Mongo query per folder
type: lesson
---

# luz-docs folder delete verifies document security classes with one limit-1 Mongo query per folder

Instead of fetching every document in a folder and calling `verifySecurityClasses` per document in Java, the folder-delete filter now runs ONE count query per folder (jsonStoreMongoService.countCollections -> [{$match: filter}, {$count: ...}]) that searches for any violating document. Mongo $count emits NO row when nothing matches, so an empty result array means no violators and any row means at least one — check result.isEmpty(), do not parse the number. A document violates when its combined (`securityClassCodes` + `inheritedSecurityClassCodes`) is non-empty AND shares no element with the tenant's classes. As a find filter:

- `securityClassCodes: {\: {\: tenantClasses}}` — array shares nothing with tenant (also matches missing/empty field, which is why the next clause exists)
- same for `inheritedSecurityClassCodes`
- `\: [{"securityClassCodes.0": {\: true}}, {"inheritedSecurityClassCodes.0": {\: true}}]` — combined non-empty; without this, class-less (public) documents would falsely violate

If the query returns anything → throw `DocumentMismatchSecurityClassCodeException`, aborting the whole delete — exactly the old Java semantics of `CollectionUtil.isMatchingSecurityClasses`. Empty tenant list works too: `\: []` matches nothing, so \ matches all → any classed doc violates, mirroring Java.

Caveat: `_deletionStatus` is matched with strict equality `"false"` (docs missing the field are excluded) to mirror the old `getAllDocumentsMetadata` criteria — do NOT swap in `buildDeletionStatusCondition`, whose \ treats a missing field as non-deleted.

Context: [[luz-docs folder delete filter double-fetched every subfolder]], [[3 Resources/Data/MongoDB/Split bulk scans on folderIds.1 exists to separate single-array-element fast path]]

## Related

- [[luz-docs folder delete filter double-fetched every subfolder]]
- [[3 Resources/Data/MongoDB/Split bulk scans on folderIds.1 exists to separate single-array-element fast path]]

%% ai-graph-start %%

**Related notes:**
- [[Validate with a count query for violators instead of loading all documents]]
- [[getCollectionMetadataByTerms silently ignored includeDeletedRecord for non-document collections]]
- [[Split bulk scans on folderIds.1 exists to separate single-array-element fast path]]
- [[luz-docs folder delete filter double-fetched every subfolder]]
- [[Folder recovery must recompute inherited security after deletion statuses are cleared]]

**Relations:**
- luz-docs folder delete — *verifies* — document security classes
- luz-docs folder delete — *uses* — Mongo query
- Mongo query — *is executed per* — folder
- Mongo query — *replaces* — old Java semantics
- old Java semantics — *involved* — fetching every document in a folder
- old Java semantics — *involved* — calling verifySecurityClasses per document
- verifySecurityClasses — *is a Java method* — 
- luz-docs folder delete — *runs* — jsonStoreMongoService.countCollections
- jsonStoreMongoService.countCollections — *searches for* — violating document
- jsonStoreMongoService.countCollections — *uses* — $match
- jsonStoreMongoService.countCollections — *uses* — $count
- $count — *emits NO row if* — nothing matches
- empty result array — *indicates* — no violators
- any row — *indicates* — at least one violator
- document — *violates if* — combined security classes is non-empty
- document — *violates if* — combined security classes shares no element with tenant's classes
- combined security classes — *is composed of* — securityClassCodes
- combined security classes — *is composed of* — inheritedSecurityClassCodes
- find filter — *includes condition for* — securityClassCodes
- find filter — *includes condition for* — inheritedSecurityClassCodes
- find filter — *checks for* — combined security classes being non-empty
- Mongo query — *throws* — DocumentMismatchSecurityClassCodeException if returns anything
- DocumentMismatchSecurityClassCodeException — *aborts* — whole delete
- DocumentMismatchSecurityClassCodeException — *mirrors semantics of* — CollectionUtil.isMatchingSecurityClasses
- empty tenant list — *causes* — any classed doc violates
- _deletionStatus — *is matched with* — strict equality 'false'
- _deletionStatus matching — *mirrors* — getAllDocumentsMetadata criteria
- buildDeletionStatusCondition — *treats* — missing field as non-deleted
- luz-docs folder delete — *is related to* — luz-docs folder delete filter double-fetched every subfolder
- luz-docs folder delete — *is related to* — Split bulk scans on folderIds.1 exists to separate single-array-element fast path

%% ai-graph-end %%