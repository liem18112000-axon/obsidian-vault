---
title: "TransactionStatus.from returns null on unknown Payrexx status causing silent NotNull 400"
created: 2026-07-31
type: lesson
status: seedling
source: "LUZ-157476 investigation 2026-07"
tags: [payrexx, luz-online-payment, luz-157476, jackson, enum, gotcha]
---

# TransactionStatus.from returns null on unknown Payrexx status causing silent NotNull 400

In `luz_online_payment`, `TransactionStatus.from(String)` (the `@JsonCreator`) loops the enum and **returns `null`** when no value matches, instead of throwing. Since `Transaction.status` is `@NotNull`, any Payrexx webhook carrying a status string not in the enum deserializes `status` to null and is rejected with a silent HTTP 400 at the bean-validation layer -- no log, and Payrexx retries then gives up.

The enum currently covers: waiting, confirmed, cancelled, declined, authorized, reserved, refunded, refund_pending, partially-refunded, chargeback, error, technical-error, uncaptured, refund-failed. Any status Payrexx adds outside this list silently breaks the webhook.

This is a candidate root cause (alongside a missing `payment`) for the ~43% 400 rejection rate observed on dev -- see [[luz_online_payment notify webhook silently 400-rejects ~43% of Payrexx webhooks on dev]].

Lesson: a `@JsonCreator` enum factory that returns null on unknown input is a silent footgun -- prefer throwing (or map to an explicit UNKNOWN member) so bad/new values fail loudly instead of turning into a downstream `@NotNull` 400.

## Related

- [[luz_online_payment notify webhook silently 400-rejects ~43% of Payrexx webhooks on dev]]
