---
ai_hash: 121a789a86037a5b
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-22
entities: []
source: session 2026-06-22 monthly->yearly PROD triage
status: seedling
tags:
- luz_store
- subscriptions
- product-config
- incident-analysis
title: luz_store product.price_plans is a joined string that constrains a subscription's
  allowed plans
type: lesson
---

# luz_store product.price_plans is a joined string that constrains a subscription's allowed plans

In luz_store, the set of price plans a product *offers* is stored in the `product.price_plans` column as a single **`Constants.PRICE_PLAN_SEPARATOR`-joined string** (e.g. `MONTHLY_YEARLY`, or just `YEARLY`), and parsed back into a `List<PricePlan>` via `PricePlan.valueOf` (`SpecificTenantPricingService.convertToProductModel`, ~line 109). `BillingConstraint` validates that a bills plan is `contains()`-ed in this list.

Implication for "wrong plan" incidents: a self-service subscribe can only carry a `pricePlan` the product actually offers, so **a product whose `price_plans` is configured yearly-only will make every new self-service subscription YEARLY by configuration** — no code bug needed. When diagnosing why a tenant ended up on YEARLY, check `SELECT price_plans FROM product WHERE id=<n>` early: if its `YEARLY` (or YEARLY-first where a default-to-first applies), the product config is the root cause and the fix is upstream (catalog/pricing config), not luz_store. Relates to [[3 Resources/Work-Kepler/luz_store/Attributing a luz_store subscription's origin from created_by, method and updated_by]].

## Related

- [[3 Resources/Work-Kepler/luz_store/Attributing a luz_store subscription's origin from created_by, method and updated_by]]

%% ai-graph-start %%

**Related notes:**
- [[A luz_store subscription changes billing period only by new-row or direct DB write]]
- [[Attributing a luz_store subscription's origin from created_by, method and updated_by]]
- [[luz_store company_uri format and marketing-code price-plan suffixes]]
- [[luz_store subscription.price_plan is ORM-immutable but bulk-SQL writable]]
- [[Hibernate Envers on luz_store SubscriptionEntity is field-scoped and omits price_plan]]

%% ai-graph-end %%