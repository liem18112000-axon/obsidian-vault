---
ai_hash: 019097680b3b7457
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-31
entities:
- TransactionStatus.from
- luz_online_payment
- Payrexx
- Transaction.status
- '@JsonCreator'
- 'null'
- '@NotNull'
- HTTP 400
- bean-validation layer
- webhook
- waiting
- confirmed
- cancelled
- declined
- authorized
- reserved
- refunded
- refund_pending
- partially-refunded
- chargeback
- error
- technical-error
- uncaptured
- refund-failed
- enum
- dev
- payment
- luz_online_payment notify webhook silently 400-rejects ~43% of Payrexx webhooks
  on dev
- UNKNOWN member
source: LUZ-157476 investigation 2026-07
status: seedling
tags:
- payrexx
- luz-online-payment
- luz-157476
- jackson
- enum
- gotcha
title: TransactionStatus.from returns null on unknown Payrexx status causing silent
  NotNull 400
type: lesson
---

# TransactionStatus.from returns null on unknown Payrexx status causing silent NotNull 400

In `luz_online_payment`, `TransactionStatus.from(String)` (the `@JsonCreator`) loops the enum and **returns `null`** when no value matches, instead of throwing. Since `Transaction.status` is `@NotNull`, any Payrexx webhook carrying a status string not in the enum deserializes `status` to null and is rejected with a silent HTTP 400 at the bean-validation layer -- no log, and Payrexx retries then gives up.

The enum currently covers: waiting, confirmed, cancelled, declined, authorized, reserved, refunded, refund_pending, partially-refunded, chargeback, error, technical-error, uncaptured, refund-failed. Any status Payrexx adds outside this list silently breaks the webhook.

This is a candidate root cause (alongside a missing `payment`) for the ~43% 400 rejection rate observed on dev -- see [[luz_online_payment notify webhook silently 400-rejects ~43% of Payrexx webhooks on dev]].

Lesson: a `@JsonCreator` enum factory that returns null on unknown input is a silent footgun -- prefer throwing (or map to an explicit UNKNOWN member) so bad/new values fail loudly instead of turning into a downstream `@NotNull` 400.

## Related

- [[luz_online_payment notify webhook silently 400-rejects ~43% of Payrexx webhooks on dev]]

%% ai-graph-start %%

**Related notes:**
- [[luz_online_payment notify webhook silently 400-rejects ~43% of Payrexx webhooks on dev]]
- [[TransactionStatus.from() swallows unknown Payrexx statuses as null]]
- [[Payrexx notify webhook dispatches to two consumers, neither forwards decline code]]
- [[luz_online_payment Payrexx webhook uses JSON-only Jackson mapper with FAIL_ON_UNKNOWN_PROPERTIES off]]
- [[luz_online_payment silently drops Payrexx decline codes]]

**Relations:**
- TransactionStatus.from — *is_in* — luz_online_payment
- TransactionStatus.from — *is_a* — @JsonCreator
- TransactionStatus.from — *returns* — null
- TransactionStatus.from — *returns_null_on_unknown_status_from* — Payrexx
- Transaction.status — *is_annotated_with* — @NotNull
- Transaction.status — *deserializes_to* — null
- null — *causes* — HTTP 400
- HTTP 400 — *occurs_at* — bean-validation layer
- Payrexx — *sends* — webhook
- webhook — *carries_status_string_not_in* — enum
- enum — *covers* — waiting
- enum — *covers* — confirmed
- enum — *covers* — cancelled
- enum — *covers* — declined
- enum — *covers* — authorized
- enum — *covers* — reserved
- enum — *covers* — refunded
- enum — *covers* — refund_pending
- enum — *covers* — partially-refunded
- enum — *covers* — chargeback
- enum — *covers* — error
- enum — *covers* — technical-error
- enum — *covers* — uncaptured
- enum — *covers* — refund-failed
- Payrexx — *adds_status_strings_outside* — enum
- status string — *breaks* — webhook
- TransactionStatus.from — *is_candidate_root_cause_for* — HTTP 400
- missing payment — *is_candidate_root_cause_for* — HTTP 400
- HTTP 400 — *has_rejection_rate* — ~43%
- rejection rate — *observed_on* — dev
- luz_online_payment notify webhook silently 400-rejects ~43% of Payrexx webhooks on dev — *is_related_to* — HTTP 400
- @JsonCreator — *should_throw_or_map_to* — UNKNOWN member

%% ai-graph-end %%