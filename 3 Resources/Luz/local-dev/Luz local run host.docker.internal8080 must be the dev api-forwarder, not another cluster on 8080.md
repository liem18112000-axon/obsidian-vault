---
title: "Luz local run: host.docker.internal:8080 must be the dev api-forwarder, not another cluster on 8080"
created: 2026-08-05
type: lesson
status: seedling
source: "session 2026-08-05 luz_docs_import public-keys 500"
tags: [luz-docs, docker-compose, kubectl, port-forward, keycloak, gotcha]
---

# Luz local run: host.docker.internal:8080 must be the dev api-forwarder, not another cluster on 8080

When running **luz_docs_import** (or any Luz service) locally via docker-compose against the dev GKE cluster, the container reaches its dev dependencies through `host.docker.internal:8080`. That host port **must** be a `kubectl port-forward` of the dev `services/api-forwarder`, which routes by path prefix (`/luzsec` → jwt-service, `/luz_docs` → luz-docs, `/luz_jsonstore`, `/luz_antivirus`, …).

## Gotcha
A separate local **`customer360-control-plane` kind cluster** also publishes host port 8080 (`0.0.0.0:8080->30080/tcp`) and serves **Keycloak**. If it is running, the luz api-forwarder port-forward cannot bind 8080, so `host.docker.internal:8080` silently reaches Keycloak instead. The two clusters cannot share 8080 — run one at a time, or remap luz to a free port.

## Symptom
JWT validation fails on every request: `GET /luzsec/api/public-keys/active` returns **404** (`{"error":"Unable to find matching target resource method"}`), `com.axonivy.sec.token.jwt.PublicKeyService` gets `RSAPublicKey null`, and the request 500s with `The RSAPublicKey cannot be null`.

## Tell-tale
`curl http://localhost:8080/` returns **302 → /admin/** (Keycloak) instead of the api-forwarder. Every `/luzsec`, `/luz_docs`, token path also 404s identically — a single backend swallowing all paths, not prefix routing.

## Fix
```bash
docker stop customer360-control-plane            # free 8080  (or remap luz to another port)
kubectl port-forward --address 0.0.0.0 services/api-forwarder 8080:8080 -n dev
```
Use `--address 0.0.0.0` so the container reaching via `host.docker.internal` can connect (a 127.0.0.1-only bind is not reachable from the container). The `luz-docs-local-run` skill sets up exactly this forward.

Related: [[JWT_SERVICE_HOST_PORT must have no trailing slash (system-properties concatenates /luzsec/api/)]]

## Related

- [[JWT_SERVICE_HOST_PORT must have no trailing slash (system-properties concatenates /luzsec/api/)]]
