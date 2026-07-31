---
title: "MicroProfile Fault Tolerance annotations are inert on self-invoked methods"
created: 2026-06-05
type: lesson
status: seedling
source: "session 2026-06-05 LUZ-154804"
tags: [microprofile, fault-tolerance, cdi, gotcha, luz-docs]
---

# MicroProfile Fault Tolerance annotations are inert on self-invoked methods

MicroProfile Fault Tolerance (@Retry, @Fallback, @Timeout, @CircuitBreaker) is implemented as CDI interceptors, and interceptors only run when a call goes through the bean's CDI proxy. A method invoked from within the same bean (`this.method(...)`) bypasses the proxy entirely — the annotations are silently inert: no retry, no fallback, no error, nothing in the logs.

Smell to check during review: an @Retry/@Fallback-annotated method whose only callers are in the same class. Confirmed in luz_docs `MaterializeCascadeService.rematerializeDocument` — annotated but only self-invoked from `onFolderParentsChange` and `rematerializeDocumentsByIds`, so the annotations never fired; we deleted them (the PARTIAL-marker passive retry is the real retry mechanism there, and in-request blocking retries with 500ms delay per doc inside a large batch would stall the request anyway).

Fixes when the retry IS wanted: route through the proxy (self-injection of the bean, or split the method into another bean), or write an explicit retry loop.

Related: [[luz_docs materialize passive retry via cascade markers]]

## Related

- [[luz_docs materialize passive retry via cascade markers]]
