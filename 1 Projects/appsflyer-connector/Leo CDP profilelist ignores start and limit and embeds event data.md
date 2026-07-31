---
title: "Leo CDP profile/list ignores start and limit and embeds event data"
created: 2026-06-30
type: lesson
status: seedling
source: "session 2026-06-30; live probes with read token"
tags: [leo-cdp, api, pagination, gotcha, events]
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
