---
ai_hash: d11e02f450abf7af
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-30
entities:
- Leo CDP
- Leo CDP Data Observer API
- datahub4dcdp.bigdatavietnam.org
- POST /api/profile/save
- POST /api/event/save
- GET /api/event/list
- HTTP 200
- 'errorCode: 0'
- 'errorCode: 500'
- No profile found
- profile id
- primaryEmail
- Write-scoped public ingestion token
- /webhook ingestion token
- Async materialization
- ingestion pipeline
- AppsFlyer S2S not in Pull
- Leo CDP dashboard
- read-capable token
- ingestion lag
- content-hash dedupe
- Identity-keyed CDP API breaks content-hash idempotency
- Leo CDP public REST API contract
source: session 2026-06-30; live push_to_data_observer.py run
status: seedling
tags:
- leo-cdp
- api
- gotcha
- eventual-consistency
- verification
title: Leo CDP save returns 200 but event/list cannot read it back
type: lesson
---

# Leo CDP save returns 200 but event/list cannot read it back

Against the live Leo CDP Data Observer API (`datahub4dcdp.bigdatavietnam.org`), `POST /api/profile/save` and `POST /api/event/save` both return **HTTP 200 with `errorCode: 0`** and mint a profile id — yet `GET /api/event/list` (by `primaryEmail` **or** by `profileId`) returns `errorCode: 500` "No profile found" immediately after, and stayed that way through 60s of polling.

Two likely, non-exclusive causes:

1. **Write-scoped public ingestion token.** Every save response carries `canEditData:false, canInsertData:false, canDeleteData:false, canSetAuthorization:false`. The `tokenkey`/`tokenvalue` pair behaves like the public `/webhook` ingestion token — allowed to *submit* data for async processing but **not** granted read/query access, so `event/list` finds nothing.
2. **Async materialization.** Saves are accepted into an ingestion pipeline, not written synchronously; a brand-new profile/event is not instantly queryable (same family of behavior as the project's 'AppsFlyer S2S not in Pull' gotcha).

**Consequence for verification:** a 200 from save means *accepted*, NOT *queryable*. Do not treat an empty `event/list` as a failed push. To truly verify, check the Leo CDP dashboard, or use a read-capable token, or allow for ingestion lag. This is also *why* a content-hash dedupe can't be confirmed client-side — see [[Identity-keyed CDP API breaks content-hash idempotency]] and [[Leo CDP public REST API contract]].

## Related

- [[Leo CDP public REST API contract]]
- [[Identity-keyed CDP API breaks content-hash idempotency]]

%% ai-graph-start %%

**Related notes:**
- [[Leo CDP public REST API contract]]
- [[Leo CDP event observerId is the pushing tokenkey and eventsave can split from profilesave identity]]
- [[Leo CDP profilelist ignores start and limit and embeds event data]]
- [[Leo CDP admin dashboard is a hash-routed SPA on a separate host from the API]]
- [[Identity-keyed CDP API breaks content-hash idempotency]]

**Relations:**
- Leo CDP Data Observer API — *is hosted at* — datahub4dcdp.bigdatavietnam.org
- POST /api/profile/save — *is an endpoint of* — Leo CDP Data Observer API
- POST /api/event/save — *is an endpoint of* — Leo CDP Data Observer API
- GET /api/event/list — *is an endpoint of* — Leo CDP Data Observer API
- POST /api/profile/save — *returns status* — HTTP 200
- POST /api/profile/save — *returns error code* — errorCode: 0
- POST /api/profile/save — *mints* — profile id
- POST /api/event/save — *returns status* — HTTP 200
- POST /api/event/save — *returns error code* — errorCode: 0
- POST /api/event/save — *mints* — profile id
- GET /api/event/list — *returns error code* — errorCode: 500
- GET /api/event/list — *returns message* — No profile found
- GET /api/event/list — *can query by* — primaryEmail
- GET /api/event/list — *can query by* — profile id
- Write-scoped public ingestion token — *is a cause for* — GET /api/event/list returns errorCode: 500
- Write-scoped public ingestion token — *has permission* — canEditData:false
- Write-scoped public ingestion token — *has permission* — canInsertData:false
- Write-scoped public ingestion token — *has permission* — canDeleteData:false
- Write-scoped public ingestion token — *has permission* — canSetAuthorization:false
- Write-scoped public ingestion token — *behaves like* — /webhook ingestion token
- Write-scoped public ingestion token — *allows* — submit data for async processing
- Write-scoped public ingestion token — *does not grant* — read/query access
- Async materialization — *is a cause for* — GET /api/event/list returns errorCode: 500
- POST /api/profile/save — *data is processed by* — ingestion pipeline
- POST /api/event/save — *data is processed by* — ingestion pipeline
- ingestion pipeline — *processes data* — asynchronously
- ingestion pipeline — *does not write data* — synchronously
- Async materialization — *causes* — profile/event not instantly queryable
- Async materialization — *is similar to* — AppsFlyer S2S not in Pull
- HTTP 200 — *from save means* — accepted
- HTTP 200 — *from save does not mean* — queryable
- empty event/list — *is not* — failed push
- Verification — *can be done via* — Leo CDP dashboard
- Verification — *can be done via* — read-capable token
- Verification — *can be done via* — ingestion lag
- Async materialization — *explains why* — content-hash dedupe cannot be confirmed client-side
- Identity-keyed CDP API breaks content-hash idempotency — *is related to* — content-hash dedupe
- Leo CDP public REST API contract — *describes* — Leo CDP Data Observer API
- Leo CDP public REST API contract — *is related to* — Leo CDP

%% ai-graph-end %%