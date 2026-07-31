---
title: "Deterministic Mongo pipeline updates return matched-not-modified; treat jsonstore SC_MULTI_STATUS as benign"
created: 2026-06-08
type: gotcha
status: seedling
source: "luz_docs materialize review #5, 2026-06-08"
tags: [mongodb, jsonstore, luz-docs, materialize, retry, gotcha]
---

# Deterministic Mongo pipeline updates return matched-not-modified; treat jsonstore SC_MULTI_STATUS as benign

A MongoDB pipeline `updateMany` that recomputes the **same** value a document already holds counts the doc as **matched but not modified** (`matchedCount > modifiedCount`). luz_jsonstore turns `matchedCount != modifiedCount` into an HTTP **207 SC_MULTI_STATUS** (`DocumentException`), which luz_docs materialize cascades must treat as a **benign no-op**, not an error.

## Why it bites
The cascade pipelines are **deterministic** — re-running produces identical writes. So under `@Retry`:
1. attempt 1 stamps half the docs, then dies;
2. attempt 2 re-matches the already-stamped docs → matched > modified → SC_MULTI_STATUS;
3. if that is wrapped as a retryable `MaterializeCascadeException`, every retry reproduces it → retries exhaust → `onCascadeFailed` restores the pre-change snapshot, **reverting already-correct sentinels**, and 500s.
It also fires on the very first attempt whenever any doc already carries the target value.

## Fix pattern
Catch `DocumentException` and short-circuit on `SC_MULTI_STATUS` before wrapping:
```java
} catch (DocumentException e) {
    if (e.getHttpStatusCode() == HttpStatus.SC_MULTI_STATUS) return; // benign: matched > modified
    throw new MaterializeCascadeException(..., e);
}
```
Both `cascadeFolderNameInDocuments` (returns false) and `cascadeFolderParentChangeInDocuments` (void, returns) in `MaterializeRepository` use this. The predicate is intentionally left inline in both (one-liner, divergent catch shapes) rather than extracted.

## Related
[[materialize-code-review]]

## Related

- [[materialize-code-review]]
