---
title: "TransactionStatus.from() swallows unknown Payrexx statuses as null"
created: 2026-07-23
type: lesson
status: seedling
source: "LUZ-157476 investigation 2026-07-23"
tags: [luz-store, payrexx, gotcha, enum]
---

# TransactionStatus.from() swallows unknown Payrexx statuses as null

`TransactionStatus.from(String)` in luz_store iterates the enum and returns **null** when the wire value matches nothing — no exception, no log. If Payrexx/KlaraPay ever introduces a new status string, the request ends up with a null status; downstream it only trips the `isMissingPayment` branch if payment is also null, otherwise the failure handling is a silent no-op.

Defensive fix candidate: log + map unknown values to a sentinel (e.g. ERROR) instead of null.

## Related
- [[luz_store TransactionStatus mirrors Payrexx API statuses plus two Klara-only values]]
- [[DECLINED status falls through invoice charge-failure handling in luz_store]]
