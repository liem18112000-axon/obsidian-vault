---
title: "Netdata v2 'No charts to display / try different dates' = time-window (often timezone), not missing data"
created: 2026-08-23
type: gotcha
tags: [netdata, monitoring, timezone, gotcha, leo-customer360]
---

# Netdata v2 'No charts to display / try different dates' = time-window (often timezone), not missing data

Netdata v2.x shows **"No charts to display. Double-check your search or filters and dates and try again with different conditions."** when the selected TIME RANGE (or a search/filter) yields nothing — even though the collector is running and data exists. It is NOT a "collector broken" message.

**Most common cause:** the dashboard time picker is set to an ABSOLUTE window that doesn't overlap the data. A newly-added collector only has data since it STARTED, and the picker interprets absolute times in the VIEWER'S timezone. E.g. a collector that began 11:38 UTC = 18:38 at UTC+7; picking "today 12:00-13:00 local" (= 05:00-06:00 UTC) shows empty.

**Fix:** use a RELATIVE window ("Last 15 minutes" / "Last 1 hour") — always includes now, timezone-proof. Clear the search box. Be on the Metrics/node view, not an empty custom "Dashboards" tab.

**Verify data actually exists (server-side, bypasses the UI):**
- retention window: GET /api/v1/chart?chart=<chart> -> first_entry / last_entry (epoch seconds).
- recent points: GET /api/v1/data?chart=<chart>&after=-900&points=6
- chart id includes the job name (e.g. redis_c360-redis.memory), NOT the bare context (redis.memory) — querying the bare context 404s.

Lesson: when a monitoring UI says "no data", check the time window + timezone + the exact metric id BEFORE assuming the pipeline is broken; confirm with the server-side API.

Source: leo-customer360 uat Netdata Redis collector, 2026-08-23.
