---
title: "Leo CDP save returns 200 but event/list cannot read it back"
created: 2026-06-30
type: lesson
status: seedling
source: "session 2026-06-30; live push_to_data_observer.py run"
tags: [leo-cdp, api, gotcha, eventual-consistency, verification]
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
