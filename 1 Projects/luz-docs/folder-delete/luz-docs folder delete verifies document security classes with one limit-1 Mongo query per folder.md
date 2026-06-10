---
title: "luz-docs folder delete verifies document security classes with one limit-1 Mongo query per folder"
created: 2026-06-10
type: lesson
status: seedling
source: "session 2026-06-10, FolderUtil.verifyFolderDocumentsSecurityClasses"
tags: [luz-docs, mongodb, security-classes, performance]
---

# luz-docs folder delete verifies document security classes with one limit-1 Mongo query per folder

Instead of fetching every document in a folder and calling `verifySecurityClasses` per document in Java, the folder-delete filter now runs ONE count query per folder (jsonStoreMongoService.countCollections -> [{$match: filter}, {$count: ...}]) that searches for any violating document. Mongo $count emits NO row when nothing matches, so an empty result array means no violators and any row means at least one — check result.isEmpty(), do not parse the number. A document violates when its combined (`securityClassCodes` + `inheritedSecurityClassCodes`) is non-empty AND shares no element with the tenant's classes. As a find filter:

- `securityClassCodes: {\: {\: tenantClasses}}` — array shares nothing with tenant (also matches missing/empty field, which is why the next clause exists)
- same for `inheritedSecurityClassCodes`
- `\: [{"securityClassCodes.0": {\: true}}, {"inheritedSecurityClassCodes.0": {\: true}}]` — combined non-empty; without this, class-less (public) documents would falsely violate

If the query returns anything → throw `DocumentMismatchSecurityClassCodeException`, aborting the whole delete — exactly the old Java semantics of `CollectionUtil.isMatchingSecurityClasses`. Empty tenant list works too: `\: []` matches nothing, so \ matches all → any classed doc violates, mirroring Java.

Caveat: `_deletionStatus` is matched with strict equality `"false"` (docs missing the field are excluded) to mirror the old `getAllDocumentsMetadata` criteria — do NOT swap in `buildDeletionStatusCondition`, whose \ treats a missing field as non-deleted.

Context: [[luz-docs folder delete filter double-fetched every subfolder]], [[Partition documents by folder membership with exact-array-match and array-index-exists filters]]

## Related

- [[luz-docs folder delete filter double-fetched every subfolder]]
- [[Partition documents by folder membership with exact-array-match and array-index-exists filters]]
