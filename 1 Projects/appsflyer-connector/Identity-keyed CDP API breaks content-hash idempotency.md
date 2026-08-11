---
ai_hash: e2081a7adce8f7fe
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-30
entities:
- Identity-keyed CDP API
- content-hash idempotency
- Leo CDP REST API
- profile identity
- targetUpdateEmail
- targetUpdatePhone
- targetUpdateCrmId
- partition-overwrite sink
- appsflyer-data-connector
- CdpHttpSink
- LeoCdpApiSink
- Leo CDP REST sink
- partition concept
- replace concept
- dedupe_key
- eventdata
- write_partition
- append_partition
- KafkaSink
- appsflyer_id
- unattachable event
- synthetic S2S appsflyer_id
- install/profile
- Identity mapping
- key_map
- unmapped keys
- applicationIDs
- Leo CDP public REST API contract
- AppsFlyer S2S not in Pull
source: session 2026-06-30; LeoCdpApiSink
status: seedling
tags:
- leo-cdp
- appsflyer
- idempotency
- gotcha
- sink
title: Identity-keyed CDP API breaks content-hash idempotency
type: lesson
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

%% ai-graph-start %%

**Related notes:**
- [[AppsFlyer Push layer appends per-event while Pull replaces the day]]
- [[Kafka sink append-only log, idempotency via dedupe_key message key]]
- [[Leo CDP event observerId is the pushing tokenkey and eventsave can split from profilesave identity]]
- [[AppsFlyer connector reduced to a single JSONL file-S3 sink]]
- [[Leo CDP public REST API contract]]

**Relations:**
- Identity-keyed CDP API — *breaks* — content-hash idempotency
- Leo CDP REST API — *resolves records by* — profile identity
- Leo CDP REST API — *does not resolve records by* — content hash
- profile identity — *includes* — targetUpdateEmail
- profile identity — *includes* — targetUpdatePhone
- profile identity — *includes* — targetUpdateCrmId
- partition-overwrite sink — *has* — cheap idempotency
- appsflyer-data-connector — *contains* — CdpHttpSink
- CdpHttpSink — *is* — Leo CDP REST sink
- LeoCdpApiSink — *was replaced by* — CdpHttpSink
- Leo CDP REST API — *has no* — partition concept
- Leo CDP REST API — *has no* — replace concept
- CdpHttpSink — *cannot do* — partition-overwrite
- CdpHttpSink — *sends* — dedupe_key
- dedupe_key — *inside* — eventdata
- CdpHttpSink — *treats* — write_partition
- write_partition — *identically to* — append_partition
- KafkaSink — *has* — append-only log
- appsflyer_id — *maps to* — no targetUpdate* field
- CdpHttpSink — *raises for* — unattachable event
- unattachable event — *has* — appsflyer_id
- appsflyer_id — *as only profile key for* — unattachable event
- synthetic S2S appsflyer_id — *has no* — real install/profile
- Identity mapping — *is configurable via* — key_map
- unmapped keys — *fold into* — applicationIDs
- appsflyer_id — *is an example of* — unmapped keys
- Leo CDP public REST API contract — *documents* — Leo CDP REST API
- AppsFlyer S2S not in Pull — *explains issue with* — synthetic S2S appsflyer_id

%% ai-graph-end %%