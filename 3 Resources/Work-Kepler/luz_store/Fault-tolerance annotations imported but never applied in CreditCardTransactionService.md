---
title: "Fault-tolerance annotations imported but never applied in CreditCardTransactionService"
created: 2026-07-23
type: observation
status: seedling
source: "LUZ-157476 investigation 2026-07-23"
tags: [luz-store, microprofile, fault-tolerance, gotcha]
---

# Fault-tolerance annotations imported but never applied in CreditCardTransactionService

CreditCardTransactionService in luz_store imports MicroProfile @Retry, @CircuitBreaker and @Bulkhead (lines 12-14) but applies NONE of them to its methods — the imports are dead. Consequence: a single transient 5xx or timeout from luz_online_payment fails the entire charge batch as TECHNICAL_ERROR with zero retry, even though the code visually suggests fault tolerance is in place.

Lesson: unused fault-tolerance imports are worse than none — they make reviewers assume resilience exists. Check for the annotations on the methods, not the import block.

## Related
- [[Payrexx declines travel in-band on HTTP 2xx in the luz charge flow]]
