---
title: "jwt-service token endpoints and replicas (Luz prod)"
created: 2026-06-30
type: concept
status: seedling
source: "PROD jwt-service investigation 2026-06-30"
tags: [luz, jwt-service, security, prod, endpoints]
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
