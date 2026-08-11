---
ai_hash: 65a04046bb266351
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-13
entities: []
source: appsflyer-data-connector project, 2026-07-13
status: seedling
tags:
- appsflyer
- attribution
- mobile-tracking
- gotcha
title: AppsFlyer only attributes events to installs recorded under the same app_id
type: lesson
---

# AppsFlyer only attributes events to installs recorded under the same app_id

AppsFlyer only treats an in-app event as attributed if its `appsflyer_id` (or matching device identifier) already has an install recorded for that exact `app_id` + `dev_key` pair. Send an event for an AFID with no matching install, and the S2S API still returns HTTP 200 — but the event is silently discarded server-side. It never shows up in the dashboard (Activity view, Events/LTV) or in the raw-data Pull API.

This is why a fabricated/random `appsflyer_id` never produces real data, and why a *real* device identifier (a genuine IDFA/GAID) does not help either if that device's install was recorded under a different app. Attribution scope is the (app_id, dev_key) pair, not the device identifier alone — a real ID from the wrong app is exactly as useless as a random one.

**Practical implication:** HTTP 200 from an AppsFlyer S2S endpoint is not evidence the event was accepted for reporting purposes — only a dashboard/Pull-API check confirms that. Confirmed empirically: 10,000 synthetic S2S events sent to a test app produced HTTP 200 for every request, then 0 conversions measured and empty Pull API CSVs for the whole date range.

## Related
- [[Minimal Android sideload technique for a real AppsFlyer install]]

## Related

- [[Minimal Android sideload technique for a real AppsFlyer install]]

%% ai-graph-start %%

**Related notes:**
- [[Minimal Android sideload technique for a real AppsFlyer install]]
- [[AppsFlyer appsflyer_id is minted at install — fabricated IDs can't round-trip through Pull API]]
- [[How to get real AppsFlyer Pull API data with the synthetic generator]]
- [[AppsFlyer Push API is the inverse of the Pull API]]
- [[AppsFlyer raw-data Pull API is plan-gated - 400 subscription error]]

%% ai-graph-end %%