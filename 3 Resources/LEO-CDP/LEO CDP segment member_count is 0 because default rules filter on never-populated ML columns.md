---
title: "LEO CDP segment member_count is 0 because default rules filter on never-populated ML columns"
created: 2026-08-23
type: lesson
status: seedling
source: "beta.leocdp.com investigation 2026-08-23"
tags: [leo-cdp, segmentation, gotcha, cdp]
---

# LEO CDP segment member_count is 0 because default rules filter on never-populated ML columns

In LEO CDP, all 8 default audience segments filter **exclusively** on four `cdp_master_profiles` columns: `predictive_clv`, `churn_risk_tier`, `customer_since`, and `last_activity_at`. The runtime pipeline never fills any of them, so every default segment shows `member_count = 0` and the "Members" column on the Segments page is all zeros.

**Why the columns stay NULL:**
- CIR (identity resolution, `resolver.py`) only writes identity/contact columns on INSERT/merge (`SCALAR_MERGE_FIELDS = full_name, email, phone_number` plus device/cookie/external ids, domain, source_systems, persona_name). It never touches analytics columns.
- `predictive_clv`/`churn_risk_tier` are ML outputs, but `backend-system/scoring` is a placeholder Dagster job with no logic.
- `last_activity_at` has a schema comment claiming a "streaming pipeline" updates it, but no such runtime writer exists in the repo.
- The only code that fills all four is the manual demo seeder `backend-system/identity_resolution/scripts/seed_full_demo_data.py`.

**Key insight:** the recompute is *correct* — `NULL > 1000`, `NULL IN (...)`, `NULL >= now()-'30d'` all evaluate to unknown, so 0 rows match. This is a data-population gap, not a frontend/RLS/recompute bug. Fix by running the demo seeder for the viewed tenant (then Refresh), implementing the scoring/lifecycle pipelines, or rewriting segment rules to reference columns CIR does populate (domain, source_systems, email IS NOT NULL).

Full write-up: `leo-customer360/docs/issues/2026-08-23-segments-member-count-all-zero.md`

See [[Diagnose stale vs genuinely-zero segment counts by comparing stored count to live matched query]].

## Related

- [[Diagnose stale vs genuinely-zero segment counts by comparing stored count to live matched query]]
