---
title: "luz_store product.price_plans is a joined string that constrains a subscription's allowed plans"
created: 2026-06-22
type: lesson
status: seedling
source: "session 2026-06-22 monthly->yearly PROD triage"
tags: [luz_store, subscriptions, product-config, incident-analysis]
---

# luz_store product.price_plans is a joined string that constrains a subscription's allowed plans

In luz_store, the set of price plans a product *offers* is stored in the `product.price_plans` column as a single **`Constants.PRICE_PLAN_SEPARATOR`-joined string** (e.g. `MONTHLY_YEARLY`, or just `YEARLY`), and parsed back into a `List<PricePlan>` via `PricePlan.valueOf` (`SpecificTenantPricingService.convertToProductModel`, ~line 109). `BillingConstraint` validates that a bills plan is `contains()`-ed in this list.

Implication for "wrong plan" incidents: a self-service subscribe can only carry a `pricePlan` the product actually offers, so **a product whose `price_plans` is configured yearly-only will make every new self-service subscription YEARLY by configuration** — no code bug needed. When diagnosing why a tenant ended up on YEARLY, check `SELECT price_plans FROM product WHERE id=<n>` early: if its `YEARLY` (or YEARLY-first where a default-to-first applies), the product config is the root cause and the fix is upstream (catalog/pricing config), not luz_store. Relates to [[3 Resources/Work-Kepler/luz_store/Attributing a luz_store subscription's origin from created_by, method and updated_by]].

## Related

- [[3 Resources/Work-Kepler/luz_store/Attributing a luz_store subscription's origin from created_by, method and updated_by]]
