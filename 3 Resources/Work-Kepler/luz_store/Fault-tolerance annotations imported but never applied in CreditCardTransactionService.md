---
ai_hash: 0393c79926e45fde
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-23
entities: []
source: LUZ-157476 investigation 2026-07-23
status: seedling
tags:
- luz-store
- microprofile
- fault-tolerance
- gotcha
title: Fault-tolerance annotations imported but never applied in CreditCardTransactionService
type: observation
---

# Fault-tolerance annotations imported but never applied in CreditCardTransactionService

CreditCardTransactionService in luz_store imports MicroProfile @Retry, @CircuitBreaker and @Bulkhead (lines 12-14) but applies NONE of them to its methods — the imports are dead. Consequence: a single transient 5xx or timeout from luz_online_payment fails the entire charge batch as TECHNICAL_ERROR with zero retry, even though the code visually suggests fault tolerance is in place.

Lesson: unused fault-tolerance imports are worse than none — they make reviewers assume resilience exists. Check for the annotations on the methods, not the import block.

## Related
- [[Payrexx declines travel in-band on HTTP 2xx in the luz charge flow]]

%% ai-graph-start %%

**Related notes:**
- [[TECHNICAL_ERROR is not retried in-flight but is retry-eligible on invoice-item rerun]]
- [[Payrexx declines travel in-band on HTTP 2xx in the luz charge flow]]
- [[Payrexx card declines reach luz_store as ERROR with prose, not DECLINED]]
- [[Payrexx notify webhook dispatches to two consumers, neither forwards decline code]]
- [[luz_online_payment silently drops Payrexx decline codes]]

%% ai-graph-end %%