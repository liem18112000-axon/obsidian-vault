---
ai_hash: f38d06335112207f
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-26
entities: []
source: session 2026-06-26; AppsFlyer help-centre + integration guides
status: seedling
tags:
- appsflyer
- push-api
- webhook
- leo-cdp
- connector
title: AppsFlyer Push API is the inverse of the Pull API
type: concept
---

# AppsFlyer Push API is the inverse of the Pull API

**AppsFlyer Push API ("streaming raw data")** is the inverse of the Pull API: instead of you polling AppsFlyer for a day's CSV, AppsFlyer **POSTs each install / in-app event to an HTTPS endpoint you expose**, in near real time, as it is attributed.

Contract that matters when building the receiver:

- **Body is JSON**, field names are the snake_case **postback/dictionary** names (`app_id`, `event_name`, `event_time`, `appsflyer_id`, `media_source`, `idfa`/`advertising_id`, …) — the same names the Pull CSV dictionary convention uses.
- **Empty fields are OMITTED** — AppsFlyer drops the key entirely rather than sending null/"". Parsers must tolerate missing keys.
- Mandatory fields always present: `app_id`, `event_name`, `event_time`, plus `idfa` (iOS) or `advertising_id` (Android).
- `event_name` discriminates the row: `install` → install event, `reinstall` → reinstall, else an in-app event named by the row's own `event_name` (e.g. `af_purchase`).
- **At-least-once delivery**: a non-2xx response (or timeout) makes AppsFlyer **retry later**. So the receiver must return 2xx only once the event is safely landed; on a transient sink failure, return 5xx (AppsFlyer redelivers) rather than dropping data.
- **Auth**: AppsFlyer lets you attach custom headers to the push — use one as a shared secret and verify it (constant-time compare).

The official support article (207034356) blocks scraping (HTTP 403); these facts came from third-party integration guides + the AppsFlyer help-centre summary. See [[AppsFlyer Push layer appends per-event while Pull replaces the day]].

## Related

- [[AppsFlyer Push layer appends per-event while Pull replaces the day]]

%% ai-graph-start %%

**Related notes:**
- [[AppsFlyer appsflyer_id is minted at install — fabricated IDs can't round-trip through Pull API]]
- [[AppsFlyer Push layer appends per-event while Pull replaces the day]]
- [[How to get real AppsFlyer Pull API data with the synthetic generator]]
- [[AppsFlyer only attributes events to installs recorded under the same app_id]]
- [[AppsFlyer raw-data Pull API is plan-gated - 400 subscription error]]

%% ai-graph-end %%