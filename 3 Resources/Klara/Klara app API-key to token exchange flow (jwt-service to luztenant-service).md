---
title: "Klara app API-key to token exchange flow (jwt-service to luztenant-service)"
created: 2026-07-17
type: concept
status: seedling
source: "session 2026-07-16/17 Klara Widget Store PROD incident"
tags: [klara, auth, jwt-service, luztenant-service, incident-response]
---

# Klara app API-key to token exchange flow (jwt-service to luztenant-service)

The Klara consumer/business app (Axon Ivy `luz-webclient`) authenticates certain backend calls (e.g. the Widget Store) by exchanging an **API key for a short-lived token**, not with the end-user JWT. The chain:

1. App page (via `ch.xpertline.luz.components.rest.ulti.RESTResource`) needs a token.
2. Calls **`jwt-service` `POST /luzsec/api/api-keys/tokens`** with an API key.
3. `jwt-service` validates the key by calling **`luztenant-service` `POST /luztenant/api/api-keys`** (`ApiKeyService.findByKey(String)`).
4. If the key is unknown/invalid, `luztenant-service` throws `ch.klara.luz.tenant.exception.ApiKeyException` → **401**; `jwt-service` surfaces that as **500** on `/api-keys/tokens`; `RESTResource` then throws `ch.xpertline.luz.components.exception.ClientException` → the Ivy page shows the generic error ("Leider ist ein unerwarteter Fehler...").

Key points for triage:
- The API key is an **app/integration credential**, NOT per-tenant. A rejected key is not a tenant problem; end-user tenants are collateral.
- The key is only in the `Authorization` header — **not logged**. `jwt-service` logs no keyId/name/clientId, and the mesh `source_workload` label is blank on those requests, so **you cannot identify the failing key from logs** — you need the api-key store (luztenant-service) or to capture the header directly.
- A **partial** failure (some `/api-keys/tokens` → 201, some → 500) that is **uniform across all luztenant-service replicas** points to a **rotated/stale key used by a subset of clients**, not a bad pod.

Observed 2026-07-16/17 PROD: Widget Store outage, ~37% of `/api-keys/tokens` exchanges failing.

## Related

- [[Per-pod breakdown of rejections separates a bad replica from a global config problem]]
- [[Klara PROD log access]]
