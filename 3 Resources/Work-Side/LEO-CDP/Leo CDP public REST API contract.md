---
ai_hash: a52ba1c0683ef542
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-30
entities: []
source: leo-cdp-api-examples repo; session 2026-06-30
status: seedling
tags:
- leo-cdp
- api
- integration
- reference
title: Leo CDP public REST API contract
type: concept
---

# Leo CDP public REST API contract

The public Leo CDP ingestion API (host `datahub4dcdp.bigdatavietnam.org`, documented by the `leo-cdp-api-examples` repo) is an **identity-keyed, one-record-per-call REST API** — not a generic batch ingest.

- **Auth**: two custom HTTP headers, `tokenkey` + `tokenvalue` (NOT `Authorization: Bearer`).
- **Endpoints**: POST `/api/profile/save`, POST `/api/event/save`, GET `/api/event/list?profileId=...` (also accepts `primaryEmail`), and a generic POST `/webhook?tokenkey=&tokenvalue=&source=`.
- **One record per request** — there is no array/batch form.

**Profile resolution** is by an identity field, not a row id:
- event/save: `targetUpdateEmail` / `targetUpdatePhone` / `targetUpdateCrmId`.
- profile/save: `updateByKey` (e.g. `crmRefId`, `primaryPhone`) + `deduplicate: false`.

**event/save key fields**: `metric` (the event name), `eventtime` (ISO-8601), `eventdata`/`jsonData` (arbitrary custom bag), commerce `tsval`/`tscur`/`tsstatus`/`tsid`/`tstax`/`scitems[]`, context `sourceip`/`useragent`/`locationName`/`tpname`/`tpurl`/`tprefurl`. An event can also upsert the profile inline (firstName, lastName, etc.).

**Gotcha**: nested objects (`extAttributes`, `applicationIDs`, `socialMediaProfiles`, `loyaltyIDs`, `incomeHistory`) are passed as **JSON-encoded strings** (`json.dumps(...)`), not native JSON objects.

See [[Identity-keyed CDP API breaks content-hash idempotency]].

## Related

- [[Identity-keyed CDP API breaks content-hash idempotency]]

%% ai-graph-start %%

**Related notes:**
- [[Leo CDP save returns 200 but eventlist cannot read it back]]
- [[Leo CDP event observerId is the pushing tokenkey and eventsave can split from profilesave identity]]
- [[Identity-keyed CDP API breaks content-hash idempotency]]
- [[Leo CDP admin dashboard is a hash-routed SPA on a separate host from the API]]
- [[Leo CDP profilelist ignores start and limit and embeds event data]]

%% ai-graph-end %%