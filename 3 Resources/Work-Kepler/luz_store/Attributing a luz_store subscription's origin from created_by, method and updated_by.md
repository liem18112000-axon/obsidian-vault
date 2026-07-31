---
title: "Attributing a luz_store subscription's origin from created_by, method and updated_by"
created: 2026-06-22
type: howto
status: seedling
source: "session 2026-06-22 monthly->yearly PROD triage"
tags: [luz_store, subscriptions, incident-analysis, billing]
---

# Attributing a luz_store subscription's origin from created_by, method and updated_by

When triaging "why does tenant X have this subscription plan?" in luz_store, the `subscription` row columns identify *who/what created it* and let you pick between the [[A luz_store subscription changes billing period only by new-row or direct DB write|two change mechanisms]]:

- `created_by` = **the tenants own email** → created through the normal **self-service subscribe flow**; luz_store stored whatever `pricePlan` the request carried, so a wrong plan originates **upstream** (webclient/booking flow or product config), not in luz_store.
- `created_by` = an admin / superuser / system identity → a **manual admin create**.
- `method = MARKETING_CODE` + `promotion_code` like `K-NN-NNNN-NN-Y` → a **marketing-code campaign** (`-Y` ⇒ YEARLY).
- `method = DEFAULT` + no promo → a plain create (self-service or admin, per `created_by`).
- `updated_by = SYSTEM` with `last_billed_time` advanced ⇒ the **billing cron** touched the row (it only writes `last_billed_time`/`actual_volume`, never the plan). A YEARLY row whose `last_billed_time` is ~1 year ahead means a full annual amount was already billed.
- `created_date` **clustered** across many tenants ⇒ a batch/script/campaign; **spread out** ⇒ individual user actions.

Corollary for reconstructing a tenants plan *over time*: use the **`billing` table** (one row per period, keyed by `company_uri`+`product_id`+`price_plan`+`billed_from`, and it survives a subscription delete), NOT `subscription_revision` — the audit table has neither `company_uri` nor `price_plan` (see [[Hibernate Envers on luz_store SubscriptionEntity is field-scoped and omits price_plan]]). A MONTHLY→YEARLY switch in the billing rows dates the real transition even after the old monthly subscription row was deleted.

## Related

- [[A luz_store subscription changes billing period only by new-row or direct DB write]]
- [[Hibernate Envers on luz_store SubscriptionEntity is field-scoped and omits price_plan]]
