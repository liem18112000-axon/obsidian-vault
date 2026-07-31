---
title: "Leo CDP public REST API contract"
created: 2026-06-30
type: concept
status: seedling
source: "leo-cdp-api-examples repo; session 2026-06-30"
tags: [leo-cdp, api, integration, reference]
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
