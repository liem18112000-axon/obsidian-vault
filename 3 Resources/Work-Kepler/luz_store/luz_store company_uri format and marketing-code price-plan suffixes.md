---
ai_hash: 1959a457c2a9f9cf
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-19
entities: []
source: session 2026-06-19 monthly->yearly investigation
status: seedling
tags:
- luz_store
- subscriptions
- reference
title: luz_store company_uri format and marketing-code price-plan suffixes
type: term
---

# luz_store company_uri format and marketing-code price-plan suffixes

luz_store identifies a tenant by `company_uri`, not a bare UUID. Two formats:
- **COMPANY**: `/luz_compensation/api/<tenantId>/companies/<n>`
- **INDIVIDUAL**: `/luz_profile/api/<tenantId>/profile`

(`Constants.LUZ_COMPENSATION_URI_PREFIX = "/luz_compensation"`, `LUZ_PROFILE_URI_PREFIX = "/luz_profile"`.) To find a tenants rows regardless of type, match `company_uri LIKE %/<tenantId>/%`. The `POST /subscriptions/running` SQL extracts the UUID back out with `substring(company_uri, 23, 36)` for COMPANY and `substring(company_uri, 18, 36)` for INDIVIDUAL.

Marketing codes (`MarketingCodeResolver`, pattern `K-NN-NNNN-NN-X`) encode the price plan in the last segment `X`: **Y→YEARLY, Q→QUARTERLY, M→MONTHLY, S→SINGLE, F→FREE, P→PAY_PER_USE, V<n>→VOLUME**. So a re-subscribe issued with a `-Y` code creates a YEARLY subscription. Relates to [[A luz_store subscription changes billing period only by new-row or direct DB write]].

## Related

- [[A luz_store subscription changes billing period only by new-row or direct DB write]]

%% ai-graph-start %%

**Related notes:**
- [[luz_store product.price_plans is a joined string that constrains a subscription's allowed plans]]
- [[Attributing a luz_store subscription's origin from created_by, method and updated_by]]
- [[A luz_store subscription changes billing period only by new-row or direct DB write]]
- [[Hibernate Envers on luz_store SubscriptionEntity is field-scoped and omits price_plan]]
- [[luz_store subscription.price_plan is ORM-immutable but bulk-SQL writable]]

%% ai-graph-end %%