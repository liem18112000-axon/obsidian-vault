---
title: "luz-docs updateManyByFilter requires every targeted document to actually change"
created: 2026-06-10
type: gotcha
status: seedling
source: "luz-docs enhance-delete-folder-api, 2026-06-10"
tags: [luz-docs, mongodb, updateMany, sprint-158]
---

# luz-docs updateManyByFilter requires every targeted document to actually change

luz-docs `JsonStoreMongoService.updateManyByFilter` enforces a strict success contract: it throws a 207 `DocumentException` unless `matchedCount == modifiedCount` AND, when the filter is `{_id: {$in: [...]}}`, both equal the number of ids. Two consequences for batch callers:

1. Every targeted document must actually CHANGE — a document that already lacks all values being removed is matched-but-unmodified and fails the whole batch. Callers must only target ids known to need the update (folder-deletion flows satisfy this because each targeted entity was discovered via the very reference being stripped).
2. It appends an increaseVersionQuery `$set` stage only for the documents collection — folder updateMany calls do NOT bump versionNumber, matching the old replaceOne behavior.

Related: [[Removing the union of array values per document is safe because absent values are no-ops]], [[MongoDB updateMany with pipeline $set + $filter is an order-preserving alternative to $pull]]

## Related

- [[Removing the union of array values per document is safe because absent values are no-ops]]
- [[MongoDB updateMany with pipeline $set + $filter is an order-preserving alternative to $pull]]
