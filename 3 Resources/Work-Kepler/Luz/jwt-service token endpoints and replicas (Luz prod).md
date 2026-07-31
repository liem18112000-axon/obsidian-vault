---
ai_hash: 2991822e776fe68b
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-30
entities: []
source: PROD jwt-service investigation 2026-06-30
status: seedling
tags:
- luz
- jwt-service
- security
- prod
- endpoints
title: jwt-service token endpoints and replicas (Luz prod)
type: concept
---

# jwt-service token endpoints and replicas (Luz prod)

`jwt-service` is the Luz **security/token** service: a GKE **Deployment** running **4 replicas** (pods `jwt-service-797dbf5654-*`) on the Istio mesh (mTLS) in the `prod` namespace of klara-prod.

**Token create/refresh endpoints** (under `/luzsec/api/`), all `POST` unless noted, called server-to-server with `Apache-HttpClient` (Java 17):
- `POST /luzsec/api/refreshtokens/tenants/{tenantId}` — per-tenant token create/refresh (the main one)
- `POST /luzsec/api/refreshtokens/swiss-post/registration`
- `POST /luzsec/api/refreshtokens/swiss-post/token-exchange` (suggests an external Swiss Post IdP dependency)
- `GET  /luzsec/api/{id}/access/tokens?type=all-tenant`

Many Luz services depend on it to mint tokens, so jwt-service latency fans out widely. See [[Luz caller read-timeout settings to jwt-service]] for the callers and their timeouts, and [[Istio DC response_flag with round latency = caller read timeout]] for how to read its access logs.

## Related

- [[Luz caller read-timeout settings to jwt-service]]
- [[Istio DC response_flag with round latency = caller read timeout]]

%% ai-graph-start %%

**Related notes:**
- [[Luz caller read-timeout settings to jwt-service]]
- [[jwt-service token path synchronously calls luztenant security-classes]]
- [[Klara app API-key to token exchange flow (jwt-service to luztenant-service)]]
- [[Downstream timeout must sit well below caller timeout (fail-fast ladder)]]
- [[luz_docs_statistic two-token model service-tenant vs per-tenant cache token]]

%% ai-graph-end %%