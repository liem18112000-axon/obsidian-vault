---
title: "Deterministic Mongo pipeline updates return matched-not-modified; treat jsonstore SC_MULTI_STATUS as benign"
created: 2026-06-08
type: gotcha
status: seedling
source: "luz_docs materialize code review, 2026-06-07 / 2026-06-08 (finding #5)"
tags: [mongodb, jsonstore, luz-jsonstore, luz-docs, materialize, retry, idempotency, gotcha]
---

# Deterministic Mongo pipeline updates return matched-not-modified; treat jsonstore SC_MULTI_STATUS as benign

A pipeline `updateMany` that recomputes the **same** value a document already holds counts it as **matched but not modified**. luz_jsonstore's `JsonStoreMongoService` turns any `matchedCount != modifiedCount` into `DocumentException` with HTTP **207 SC_MULTI_STATUS** — which an idempotent cascade must treat as a **benign no-op**, never as a retryable error.

**Why wrapping it as retryable is a trap.** The pipelines are deterministic, so every retry reproduces the same result:
1. attempt 1 stamps half the docs, then dies;
2. attempt 2 re-matches the already-stamped docs → matched > modified → 207;
3. wrapped as a retryable `MaterializeCascadeException`, retries exhaust → `onCascadeFailed` restores the pre-change snapshot, **reverting already-correct sentinels**, and 500s.

It also fires on the first attempt whenever any doc already carries the target value.

**Fix** — short-circuit on the status before wrapping:
```java
} catch (DocumentException e) {
    if (e.getHttpStatusCode() == HttpStatus.SC_MULTI_STATUS) return; // benign: matched > modified
    throw new MaterializeCascadeException(..., e);
}
```
`cascadeFolderNameInDocuments` (returns false) and `cascadeFolderParentChangeInDocuments` (void) in `MaterializeRepository` both do this; the rename cascade got it right, the parent-change cascade got it wrong in sprint-156.

**Precondition:** this is only sound when the update filter is tight — see [[Tight updateMany filter makes HTTP 207 a reliable partial-write signal]]. Under a loose filter, swallowing 207 also swallows real partial writes.

## Related

- [[Tight updateMany filter makes HTTP 207 a reliable partial-write signal]]
- [[materialize-code-review]]
- [[DistributionCacheException.isNotFound is inverted in luz_docs]]
