---
title: "Diagnose stale vs genuinely-zero segment counts by comparing stored count to live matched query"
created: 2026-08-23
type: howto
status: seedling
source: "beta.leocdp.com investigation 2026-08-23"
tags: [leo-cdp, segmentation, debugging, rls]
---

# Diagnose stale vs genuinely-zero segment counts by comparing stored count to live matched query

When a cached/materialized count looks wrong (e.g. a segment's stored `member_count`), determine whether it is **stale** or **genuinely zero** by comparing the stored value against a **live** re-evaluation of the same rule — don't assume the count is broken.

In LEO CDP specifically: the Segments **list** shows the stored `cdp_segments.member_count` (written by the Dagster recompute), while a segment's **detail page** runs the rule live via `GET /segments/{id}/matched-profiles`. If the live matched-profiles table is also empty, the stored 0 is correct (the rule truly matches nothing); if the live query returns rows but the stored count is 0, the recompute is stale/failing.

Two extra gotchas when interpreting these counts:
- Both the stored count and the live query are **RLS-scoped per tenant** (`SET app.tenant_id` → policy `tenant_id = NULLIF(btrim(current_setting('app.tenant_id', true)),'')::uuid`). Demo data seeded for tenant A shows 0 when viewing tenant B.
- The batch recompute (`recompute_all_active_segments`) has **no per-segment try/except**, so one segment with invalid `sql_rules` aborts the whole transaction and leaves *every* `member_count` unwritten — a separate failure mode from 'rules match nothing'.

This stored-vs-live comparison is the fastest first diagnostic step for any 'the count is wrong' report.

See [[LEO CDP segment member_count is 0 because default rules filter on never-populated ML columns]].

## Related

- [[LEO CDP segment member_count is 0 because default rules filter on never-populated ML columns]]
