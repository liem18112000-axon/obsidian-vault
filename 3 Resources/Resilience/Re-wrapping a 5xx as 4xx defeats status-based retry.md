---
title: "Re-wrapping a 5xx as 4xx defeats status-based retry"
created: 2026-08-03
type: lesson
status: seedling
source: "PROD investigation 2026-08-03 (invoice PDF 503)"
tags: [retry, gotcha, http-status, fault-tolerance, luz-store]
---

# Re-wrapping a 5xx as 4xx defeats status-based retry

A `catch` block that converts an upstream HTTP **5xx** into a **4xx** silently disables any retry layer that decides retryability from the HTTP status code. Retry interceptors typically retry 500/502/503/504 and treat 4xx as a permanent client error — so if you catch the callee's 503 and rethrow it as `400 BAD_REQUEST`, the interceptor sees a 4xx and gives up on the first attempt. A transient blip becomes a hard, permanent failure, and the retry code looks like it works but never fires.

## Where it bit us
luz-store `LuzDocsCreatorIvyRestClientService.createDocument()` is annotated `@InvoiceRunV2Retryable` (interceptor retries 5xx, 3 attempts, exponential backoff <=30s). But its body did:

```java
catch (Exception e) {
    throw new ClientErrorException("Can not create document! ... status code 503", Status.BAD_REQUEST);
}
```

So the Ivy engine's real 503 was reshaped into a 400 *before* the interceptor's `isRetryable()` ran. Result: a momentary `luz-webclient` overload during an invoice run permanently marked invoices `PDF_CREATED_FAILED` — the retry never happened. (PROD, 2026-08-03, invoice run 8751ac56.)

## The rule
- When rethrowing across a retry boundary, **preserve the upstream status** (if the cause is a `WebApplicationException`, rethrow with its original status; only map genuine client errors to 4xx), **or**
- make the retry predicate inspect the **original cause chain**, not just the top exception's status.
- Watch the tell: an error *message* that says "503" while the thrown exception *type/status* is 400 — that mismatch is the smell.

## Related
[[Invoice Run v2 PDF creation flow]] [[MicroProfile Fault Tolerance retry]]

## Related

- [[Invoice Run v2 PDF creation flow]]
- [[MicroProfile Fault Tolerance retry]]
