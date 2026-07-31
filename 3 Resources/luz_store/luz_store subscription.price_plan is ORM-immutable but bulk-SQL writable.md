---
title: "luz_store subscription.price_plan is ORM-immutable but bulk-SQL writable"
created: 2026-06-19
type: lesson
status: seedling
source: "session 2026-06-19 monthly->yearly investigation"
tags: [luz_store, hibernate, jpa, gotcha, subscriptions]
---

# luz_store subscription.price_plan is ORM-immutable but bulk-SQL writable

In luz_store, `SubscriptionEntity.price_plan` is mapped `@Column(name="price_plan", nullable=false, updatable=false)`. Hibernate **entity flush** (the UPDATE generated from a managed entity via `merge`/dirty-check) therefore **never** writes `price_plan` — the column is silently excluded from every UPDATE statement.

The gotcha: `updatable=false` only constrains the ORM flush path. **JPQL bulk `CriteriaUpdate`, native SQL, and Flyway/ad-hoc SQL all BYPASS it** and can freely write `price_plan`. (Same applies to the also-immutable `product_id` join column.)

Practical implication for incident analysis: if a subscription row appears with a *changed* `price_plan` (same `id`), it cannot have come from application ORM code — it must be a non-ORM write (a hand-run SQL/`UPDATE`, a bulk DAO update, or a migration). Conversely, the REST PATCH `partiallyUpdate()` also excludes `/pricePlan` from its allow-list, and the recurring billing cron only reads the plan. See [[A luz_store subscription changes billing period only by new-row or direct DB write]].

## Related

- [[A luz_store subscription changes billing period only by new-row or direct DB write]]
- [[Hibernate Envers on luz_store SubscriptionEntity is field-scoped and omits price_plan]]
