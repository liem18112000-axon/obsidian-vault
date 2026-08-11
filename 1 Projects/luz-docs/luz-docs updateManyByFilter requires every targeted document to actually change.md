---
ai_hash: 5dd112614c8bc6a0
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-10
entities:
- luz-docs
- JsonStoreMongoService.updateManyByFilter
- DocumentException
- matchedCount
- modifiedCount
- _id
- ids
- batch callers
- document
- values
- folder-deletion flows
- entity
- reference
- increaseVersionQuery $set stage
- documents collection
- folder updateMany calls
- versionNumber
- replaceOne
- Removing the union of array values per document is safe because absent values are
  no-ops
- MongoDB updateMany with pipeline $set + $filter is an order-preserving alternative
  to $pull
- MongoDB
- $set
- $filter
- $pull
- luz-docs updateManyByFilter requires every targeted document to actually change
source: luz-docs enhance-delete-folder-api, 2026-06-10
status: seedling
tags:
- luz-docs
- mongodb
- updateMany
- sprint-158
title: luz-docs updateManyByFilter requires every targeted document to actually change
type: gotcha
---

# luz-docs updateManyByFilter requires every targeted document to actually change

luz-docs `JsonStoreMongoService.updateManyByFilter` enforces a strict success contract: it throws a 207 `DocumentException` unless `matchedCount == modifiedCount` AND, when the filter is `{_id: {$in: [...]}}`, both equal the number of ids. Two consequences for batch callers:

1. Every targeted document must actually CHANGE — a document that already lacks all values being removed is matched-but-unmodified and fails the whole batch. Callers must only target ids known to need the update (folder-deletion flows satisfy this because each targeted entity was discovered via the very reference being stripped).
2. It appends an increaseVersionQuery `$set` stage only for the documents collection — folder updateMany calls do NOT bump versionNumber, matching the old replaceOne behavior.

Related: [[Removing the union of array values per document is safe because absent values are no-ops]], [[MongoDB updateMany with pipeline $set + $filter is an order-preserving alternative to $pull]]

## Related

- [[Removing the union of array values per document is safe because absent values are no-ops]]
- [[MongoDB updateMany with pipeline $set + $filter is an order-preserving alternative to $pull]]

%% ai-graph-start %%

**Related notes:**
- [[Removing the union of array values per document is safe because absent values are no-ops]]
- [[MongoDB updateMany with pipeline $set + $filter is an order-preserving alternative to $pull]]
- [[luz_docs bulk updateMany recompute is set-based - one event, batched literal-table pipeline, not per-doc fan-out]]
- [[luz_docs change tracking covers updateMany-deleteMany via projected before-after snapshots keyed by id]]
- [[luz-docs delete-folder batching roadmap - remaining per-item paths]]

**Relations:**
- luz-docs updateManyByFilter requires every targeted document to actually change — *describes* — JsonStoreMongoService.updateManyByFilter
- JsonStoreMongoService.updateManyByFilter — *is_part_of* — luz-docs
- JsonStoreMongoService.updateManyByFilter — *throws* — DocumentException
- DocumentException — *is_code* — 207
- JsonStoreMongoService.updateManyByFilter — *enforces* — matchedCount == modifiedCount
- JsonStoreMongoService.updateManyByFilter — *enforces* — matchedCount == number of ids
- JsonStoreMongoService.updateManyByFilter — *enforces* — modifiedCount == number of ids
- JsonStoreMongoService.updateManyByFilter — *has_consequences_for* — batch callers
- batch callers — *must_ensure* — document changes
- document — *lacks* — values
- document — *is_status* — matched-but-unmodified
- matched-but-unmodified — *causes* — batch failure
- folder-deletion flows — *satisfy* — update requirement
- entity — *discovered_via* — reference
- JsonStoreMongoService.updateManyByFilter — *appends* — increaseVersionQuery $set stage
- increaseVersionQuery $set stage — *targets* — documents collection
- folder updateMany calls — *do_not_bump* — versionNumber
- folder updateMany calls — *matches_behavior_of* — replaceOne
- luz-docs updateManyByFilter requires every targeted document to actually change — *is_related_to* — Removing the union of array values per document is safe because absent values are no-ops
- luz-docs updateManyByFilter requires every targeted document to actually change — *is_related_to* — MongoDB updateMany with pipeline $set + $filter is an order-preserving alternative to $pull
- MongoDB updateMany with pipeline $set + $filter is an order-preserving alternative to $pull — *uses* — $set
- MongoDB updateMany with pipeline $set + $filter is an order-preserving alternative to $pull — *uses* — $filter
- MongoDB updateMany with pipeline $set + $filter is an order-preserving alternative to $pull — *is_alternative_to* — $pull
- JsonStoreMongoService.updateManyByFilter — *uses_filter_on* — _id

%% ai-graph-end %%