---
ai_hash: 28293b65f8c5b8be
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-23
entities: []
source: LUZ-157476 investigation 2026-07-23
status: seedling
tags:
- luz-store
- payrexx
- gotcha
- enum
title: TransactionStatus.from() swallows unknown Payrexx statuses as null
type: lesson
---

# TransactionStatus.from() swallows unknown Payrexx statuses as null

`TransactionStatus.from(String)` in luz_store iterates the enum and returns **null** when the wire value matches nothing — no exception, no log. If Payrexx/KlaraPay ever introduces a new status string, the request ends up with a null status; downstream it only trips the `isMissingPayment` branch if payment is also null, otherwise the failure handling is a silent no-op.

Defensive fix candidate: log + map unknown values to a sentinel (e.g. ERROR) instead of null.

## Related
- [[luz_store TransactionStatus mirrors Payrexx API statuses plus two Klara-only values]]
- [[DECLINED status falls through invoice charge-failure handling in luz_store]]

%% ai-graph-start %%

**Related notes:**
- [[TransactionStatus.from returns null on unknown Payrexx status causing silent NotNull 400]]
- [[DECLINED status falls through invoice charge-failure handling in luz_store]]
- [[luz_store TransactionStatus mirrors Payrexx API statuses plus two Klara-only values]]
- [[Payrexx card declines reach luz_store as ERROR with prose, not DECLINED]]
- [[transaction_status column stores Java enum names not JSON wire values]]

%% ai-graph-end %%