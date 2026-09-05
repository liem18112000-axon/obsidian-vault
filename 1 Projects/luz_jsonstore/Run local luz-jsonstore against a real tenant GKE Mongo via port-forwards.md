---
title: "Run local luz-jsonstore against a real tenant GKE Mongo via port-forwards"
created: 2026-08-25
type: howto
status: seedling
source: "session 2026-08-25"
tags: [luz-jsonstore, mongodb, kubectl, port-forward, docker]
---

# Run local luz-jsonstore against a real tenant GKE Mongo via port-forwards

To point a locally-run luz-jsonstore (docker-compose) at a real tenant's data in dev GKE:
1. Tenant's mongo cluster = luz-mongodb0N where N = (first hex char of tenantId) mod 4, namespace dev-mongodb-clusters.
2. The app connects DIRECT (no replicaSet in the URI), so forward the PRIMARY member. Find it with an unauthenticated `db.hello().primary` exec into any rs pod (mongosh in the mongod container).
3. kubectl port-forward pod/luz-mongodb0N-cluster-rs-0 27017:27017.
4. Also forward api-forwarder (security) 8080:8080 and luz-vault 8200:8200 from namespace dev.
5. In docker-compose set CH_KLARA_JSONSTORE_RS00 / RS01 / CREATE_DB_RS / OPS_RS to host.docker.internal:27017, and LUZ_SEC_HOST_PORT / LUZ_VAULT_HOST_PORT to host.docker.internal:8080 / 8200.

The container reaches host-bound port-forwards via host.docker.internal on Docker Desktop. getPassword needs vault reachable; requests need a bearer token (luz-skill-get-token issues an all-tenant token through the 8080 forward). If the replica set fails over, re-forward the new primary.

## Related

- [[Serving a custom applicationbson media type in JAX-RS via MessageBodyReader and Writer]]
