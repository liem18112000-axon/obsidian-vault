---
ai_hash: 510c6a8afb23f0b6
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-19
entities: []
source: session 2026-06-19 monthly->yearly investigation
status: seedling
tags:
- luz_store
- hibernate
- envers
- audit
- gotcha
title: Hibernate Envers on luz_store SubscriptionEntity is field-scoped and omits
  price_plan
type: lesson
---

# Hibernate Envers on luz_store SubscriptionEntity is field-scoped and omits price_plan

Hibernate Envers auditing on luz_store `SubscriptionEntity` is **field-scoped, not class-scoped**: only `bought_volume`, `actual_volume`, and `comment` (plus the `BaseEntity` audit fields) carry `@Audited`. 

Consequently the audit table is named `subscription_revision` (NOT the conventional `subscription_aud`; companion table `revision_info(rev, rev_time)`), and its columns are just `id, rev, revtype, created_by, created_date, updated_by, updated_date, actual_volume, comment, bought_volume`.

The gotcha: `subscription_revision` has **no `price_plan`, `subscription_until`, `unsubscribed_date`, `method`, or `promotion_code`** column — so the audit trail **cannot prove or disprove a plan change or an unsubscribe**. `revtype` (0=ADD, 1=MOD, 2=DEL) joined to `revision_info.rev_time` only timestamps row create/modify/delete events. For plan-change forensics use `created_date`/`created_by`/`method` on the live `subscription` rows, `billing.price_plan` over time, and external DB/app logs instead. Relates to [[luz_store subscription.price_plan is ORM-immutable but bulk-SQL writable]].

## Related

- [[luz_store subscription.price_plan is ORM-immutable but bulk-SQL writable]]

%% ai-graph-start %%

**Related notes:**
- [[luz_store subscription.price_plan is ORM-immutable but bulk-SQL writable]]
- [[A luz_store subscription changes billing period only by new-row or direct DB write]]
- [[Attributing a luz_store subscription's origin from created_by, method and updated_by]]
- [[luz_store product.price_plans is a joined string that constrains a subscription's allowed plans]]
- [[luz_store company_uri format and marketing-code price-plan suffixes]]

%% ai-graph-end %%