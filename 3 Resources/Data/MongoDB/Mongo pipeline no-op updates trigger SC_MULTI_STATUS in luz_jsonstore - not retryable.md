---
title: "Mongo pipeline no-op updates trigger SC_MULTI_STATUS in luz_jsonstore - not retryable"
created: 2026-06-07
type: lesson
status: seedling
source: "materialize code review 2026-06-07"
tags: [mongodb, luz-jsonstore, luz-docs, retry, idempotency]
---

# Mongo pipeline no-op updates trigger SC_MULTI_STATUS in luz_jsonstore - not retryable

MongoDB pipeline-style updates (`updateMany` with aggregation pipeline) that recompute values identical to what a document already holds count that document as **matched but not modified**. luz_jsonstore's JsonStoreMongoService then throws `DocumentException(SC_MULTI_STATUS)` whenever `matchedCount != modifiedCount`.

Consequence: wrapping that 207 into a retryable exception is a trap — the pipeline is deterministic, so every retry reproduces matched > modified, retries exhaust, and any fallback (e.g. snapshot rollback) fires against documents that were actually updated correctly. Deterministic trigger: retry-after-partial-success — attempt 1 updates half then dies; attempt 2 sees those docs as matched-not-modified.

Rule: for idempotent cascades over jsonstore updateMany, treat SC_MULTI_STATUS (matched>modified, no per-entry expectations) as a benign no-op, not an error. luz_docs rename cascade does this right (`if (e.getHttpStatusCode() == SC_MULTI_STATUS) return false;`); the parent-change cascade got it wrong in sprint-156.

## Related

- [[DistributionCacheException.isNotFound is inverted in luz_docs]]
