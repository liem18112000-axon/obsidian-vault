---
ai_hash: 70c50c4166e11aa2
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-26
entities: []
source: session 2026-06-26
status: seedling
tags:
- appsflyer
- pull-api
- s2s
- generator
- leo-cdp
title: How to get real AppsFlyer Pull API data with the synthetic generator
type: howto
---

# How to get real AppsFlyer Pull API data with the synthetic generator

To get **real rows back from the AppsFlyer Pull API** while still using the synthetic generator, the events must reference a **real `appsflyer_id`** (see [[AppsFlyer appsflyer_id is minted at install — fabricated IDs can't round-trip through Pull API]]). Three paths, most-faithful first:

1. **Real device/emulator with the AppsFlyer SDK** (debug build, dev mode on). The SDK registers a genuine install → mints a real `appsflyer_id` → SDK-fired in-app events appear in Pull. Fully faithful; needs the actual app + SDK.
2. **Register test devices** in the AppsFlyer dashboard and use its built-in **in-app event generator**, which fires against a real *test* `appsflyer_id` — those events do land in raw export.
3. **Hybrid (keeps our generator useful):** capture a real `appsflyer_id` from path 1 or 2, then drive **synthetic volume** through the generator's S2S sender against that real ID instead of an invented one. Today the generator's S2S mode fabricates IDs; this needs a new flag (e.g. `--appsflyer-ids <file>`) so `row_to_s2s_payload` uses supplied real IDs.

Two caveats that bite even when you do it right:
- **Pull API latency** — in-app-event raw data is not instant (minutes to hours); pulling immediately can still show 0.
- **Timezone-sensitive** — pull the correct day in the *app's configured* AppsFlyer timezone (`Asia/Ho_Chi_Minh` for this app), or you query an empty window.

## Related

- [[AppsFlyer appsflyer_id is minted at install — fabricated IDs can't round-trip through Pull API]]

%% ai-graph-start %%

**Related notes:**
- [[AppsFlyer appsflyer_id is minted at install — fabricated IDs can't round-trip through Pull API]]
- [[AppsFlyer only attributes events to installs recorded under the same app_id]]
- [[Minimal Android sideload technique for a real AppsFlyer install]]
- [[AppsFlyer Push API is the inverse of the Pull API]]
- [[AppsFlyer raw-data Pull API is plan-gated - 400 subscription error]]

%% ai-graph-end %%