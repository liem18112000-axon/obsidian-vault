---
title: "Identity-keyed CDP API breaks content-hash idempotency"
created: 2026-06-30
type: lesson
status: seedling
source: "session 2026-06-30; LeoCdpApiSink"
tags: [leo-cdp, appsflyer, idempotency, gotcha, sink]
---

# Identity-keyed CDP API breaks content-hash idempotency

Because the Leo CDP REST API resolves records by **profile identity** (`targetUpdateEmail`/`targetUpdatePhone`/`targetUpdateCrmId`) rather than by a content hash, a sink that posts to it loses the cheap idempotency a partition-overwrite sink has.

In `appsflyer-data-connector`, the connector's `CdpHttpSink` **is** the Leo CDP REST sink (we merged the two — there is no separate `LeoCdpApiSink`; the old generic batch+`Bearer`+`replace:true` version was replaced wholesale). An earlier partition-overwrite design made a re-pull safe by sending `replace: true` on the first batch so the server atomically cleared the partition first. The real Leo CDP API has **no partition / replace concept**, so `CdpHttpSink` **cannot** do that: a re-pulled day re-POSTs every event, and whether duplicates collapse is entirely up to the server's identity-based upsert.

**Decisions made for `CdpHttpSink` (the Leo CDP REST sink):**
- Send the envelope's `dedupe_key` inside `eventdata` so a dedup-aware backend (or downstream audit) can still collapse re-posts.
- `write_partition` and `append_partition` behave **identically** — there is no partition to clear (same as `KafkaSink`, whose append-only log also has nothing to clear).
- An event whose only profile key is `appsflyer_id` maps to **no** `targetUpdate*` field, so the sink **raises** rather than POST an unattachable event. This directly reflects the gotcha that synthetic S2S `appsflyer_id`s have no real install/profile behind them (see project memory 'AppsFlyer S2S not in Pull').

Identity mapping is configurable via `key_map`; unmapped keys (e.g. `appsflyer_id`) fold into the inline profile's `applicationIDs` as `{key}-{value}`.

See [[Leo CDP public REST API contract]].

## Related

- [[Leo CDP public REST API contract]]
