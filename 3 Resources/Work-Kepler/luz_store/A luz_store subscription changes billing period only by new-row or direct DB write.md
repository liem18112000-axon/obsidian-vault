---
title: "A luz_store subscription changes billing period only by new-row or direct DB write"
created: 2026-06-19
type: model
status: seedling
source: "session 2026-06-19 monthly->yearly investigation"
tags: [luz_store, subscriptions, billing, incident-analysis]
---

# A luz_store subscription changes billing period only by new-row or direct DB write

A luz_store subscription can only *appear* to "change billing period" (e.g. MONTHLY → YEARLY) in two ways, and they are distinguishable from the data:

- **(A) New-row create + old-row ended.** A brand-new `subscription` row is INSERTed with the new `price_plan` (via marketing-code `-Y`, manual/admin create, or re-subscribe), and the old row is ended by setting `subscription_until`/`unsubscribed_date`. The YEARLY value lives on a **new `subscription.id`**; the old MONTHLY row keeps `price_plan=MONTHLY`. This is the only code-supported path. (`product_id` is also `updatable=false`, and `id` is IDENTITY-generated, so `persist()` is always an INSERT.)
- **(B) Direct/bulk DB write in place.** Raw SQL / `CriteriaUpdate` / native UPDATE flips `price_plan` on the **same `subscription.id`** (possible only because these bypass ORM immutability — see [[luz_store subscription.price_plan is ORM-immutable but bulk-SQL writable]]). No new row, no `unsubscribed_date`, and no audit trace ([[Hibernate Envers on luz_store SubscriptionEntity is field-scoped and omits price_plan]]).

Diagnostic key: **is the new plan on a new id (A) or the original id (B)?** For a cohort of N tenants flipped at once, a shared `created_date` on N new rows ⇒ scripted creates/campaign (A); a shared `updated_date` on N in-place rows ⇒ single bulk UPDATE / ad-hoc migration (B). The recurring billing cron (`TimeBasedBillingService`) is NOT a mechanism — it only reads the plan and writes billing rows + `last_billed_time`.

## Related

- [[luz_store subscription.price_plan is ORM-immutable but bulk-SQL writable]]
- [[Hibernate Envers on luz_store SubscriptionEntity is field-scoped and omits price_plan]]
