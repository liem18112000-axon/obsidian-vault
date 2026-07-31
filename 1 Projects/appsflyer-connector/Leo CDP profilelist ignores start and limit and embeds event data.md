---
ai_hash: ffbfde9a29440a9c
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-30
entities:
- Leo CDP
- Data Observer API
- /api/event/list
- /api/profile/list
- lastTrackingEvent
- behavioralEvents
- eventStatistics
- funnelStage
- funnelStageTimeline
- inJourneyMaps
- pagination
- start parameter
- limit parameter
- AppsFlyer
- maximum_rows
- examples/list_all_events.py
- Leo CDP public REST API contract
- 1 Projects/appsflyer-connector/Leo CDP save returns 200 but eventlist cannot read
  it back
- profileId
- errorCode 500
- No profile found
- metricName
- metricValue
- createdAt
- observerId
- isConversion
- journeyId
- metric
- segment_id
- event data
- profiles
- list all events endpoint
- paging loop
- NEW ids
source: session 2026-06-30; live probes with read token
status: seedling
tags:
- leo-cdp
- api
- pagination
- gotcha
- events
title: Leo CDP profile/list ignores start and limit and embeds event data
type: lesson
---

# Leo CDP profile/list ignores start and limit and embeds event data

On the Leo CDP Data Observer API there is **no global "list all events" endpoint**, and `GET /api/event/list?profileId=...` reliably returns `errorCode 500` "No profile found" even for profile ids that `/api/profile/list` just returned. So the practical way to read events is **`GET /api/profile/list`**, which embeds each profile's event data:

- `lastTrackingEvent` — the most recent event (full object: metricName, metricValue, createdAt, observerId, isConversion, ...)
- `behavioralEvents` — list of metric names the profile has fired
- `eventStatistics` — per-`{journeyId}-{metric}` counts, e.g. `{"<journey>-mobile_install": 4}`
- `funnelStage`, `funnelStageTimeline`, `inJourneyMaps`

**Gotcha — pagination is fake:** `profile/list?segment_id=&start=&limit=` **ignores both `start` and `limit`**; every call returns the same fixed page (~10 most-recent profiles). Probed start=0/10/20/30 and limit=5/10/50/100 — all returned the identical 10 ids. So you cannot page to more than that top set with this token; a paging loop must stop when it sees no NEW ids or it loops forever. (Same family as the AppsFlyer `maximum_rows` floor: a limit param that is silently overridden.)

Implemented in `examples/list_all_events.py`. Related: [[Leo CDP public REST API contract]], [[1 Projects/appsflyer-connector/Leo CDP save returns 200 but eventlist cannot read it back]].

## Related

- [[Leo CDP public REST API contract]]
- [[1 Projects/appsflyer-connector/Leo CDP save returns 200 but eventlist cannot read it back]]

%% ai-graph-start %%

**Related notes:**
- [[Leo CDP save returns 200 but eventlist cannot read it back]]
- [[Leo CDP event observerId is the pushing tokenkey and eventsave can split from profilesave identity]]
- [[Leo CDP admin dashboard is a hash-routed SPA on a separate host from the API]]
- [[Leo CDP public REST API contract]]
- [[Identity-keyed CDP API breaks content-hash idempotency]]

**Relations:**
- Leo CDP — *has API* — Data Observer API
- Data Observer API — *provides endpoint* — /api/event/list
- Data Observer API — *provides endpoint* — /api/profile/list
- Leo CDP — *lacks* — list all events endpoint
- /api/event/list — *returns error* — errorCode 500
- /api/event/list — *returns message* — No profile found
- /api/event/list — *requires parameter* — profileId
- /api/profile/list — *embeds* — lastTrackingEvent
- /api/profile/list — *embeds* — behavioralEvents
- /api/profile/list — *embeds* — eventStatistics
- /api/profile/list — *embeds* — funnelStage
- /api/profile/list — *embeds* — funnelStageTimeline
- /api/profile/list — *embeds* — inJourneyMaps
- /api/profile/list — *ignores parameter* — start parameter
- /api/profile/list — *ignores parameter* — limit parameter
- pagination — *is fake for* — /api/profile/list
- AppsFlyer — *has parameter* — maximum_rows
- maximum_rows — *is silently overridden* — AppsFlyer
- examples/list_all_events.py — *implements* — paging loop
- lastTrackingEvent — *includes field* — metricName
- lastTrackingEvent — *includes field* — metricValue
- lastTrackingEvent — *includes field* — createdAt
- lastTrackingEvent — *includes field* — observerId
- lastTrackingEvent — *includes field* — isConversion
- behavioralEvents — *lists* — metricName
- eventStatistics — *counts by* — journeyId
- eventStatistics — *counts by* — metric
- Leo CDP public REST API contract — *is related to* — Leo CDP
- 1 Projects/appsflyer-connector/Leo CDP save returns 200 but eventlist cannot read it back — *is related to* — Leo CDP
- /api/profile/list — *is practical way to read* — event data
- /api/profile/list — *returns* — profiles
- profiles — *contain* — event data
- paging loop — *must stop when it sees no* — NEW ids
- paging loop — *avoids* — loops forever

%% ai-graph-end %%