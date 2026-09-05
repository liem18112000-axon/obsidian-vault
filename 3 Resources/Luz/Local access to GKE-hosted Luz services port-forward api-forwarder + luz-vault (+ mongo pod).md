---
title: "Local access to GKE-hosted Luz services: port-forward api-forwarder + luz-vault (+ mongo pod)"
created: 2026-08-10
type: howto
status: seedling
source: "session 2026-08-10 (luz-docs-import-api-test skill)"
tags: [luz, kubectl, port-forward, gke, mongodb, vault, ops]
---

# Local access to GKE-hosted Luz services: port-forward api-forwarder + luz-vault (+ mongo pod)

To hit a GKE-hosted Luz REST API from your laptop, `kubectl port-forward` two things (the standard wiring, e.g. luz-docs-local-run + luz-docs-import-api-test):

- **`services/api-forwarder`** local `8080`:8080 (add `--address 0.0.0.0`) — the entrypoint that routes `http://localhost:8080/<context-root>/api/...` to the right service (`luzsec`, `luz_docs_import`, `luz_docs`, ...).
- **`service/luz-vault`** local `8200`:8200 — secrets backend the services depend on.

Both `-n <ENV>` where ENV is the app namespace (`dev`, `dev-staging`, `performance`, `test`, `swissdec`, `prod`). Forwards are **idempotent**: probe `localhost:<port>` via bash `/dev/tcp` first and reuse if already open. Leave them running for reuse across skills (luz-skill-get-token reuses the 8080 forward).

**Cluster vs env:** the app namespace picks the ENV; the *cluster* is chosen by the current `kubectl` context. All non-prod envs live in **`klara-nonprod`**, so switching between dev/performance/test is just a namespace change, same context. These skills do **not** switch contexts — they print `kubectl config current-context` so you can sanity-check before anything talks to the cluster.

**MongoDB is different:** you forward a replica **pod** (`luz-mongodbXX-cluster-rs-{0,1,2}`, `27017`:27017) in the **`dev-mongodb-clusters`** namespace — NOT a service, NOT the app namespace — then probe each replica for the primary (a throwaway insert+drop). Cluster index XX is derived from the tenant id (`first hex char % 4`). Pattern from earchive-data-clean / luz-skill-materialize-stats. Related: [[Trace Luz per-service latency via the time-consuming= log marker]], [[Luz docs-import zip flow: upload-zip returns job-id, poll GET until DONE]].

## Related

- [[Trace Luz per-service latency via the time-consuming= log marker]]
- [[Luz docs-import zip flow: upload-zip returns job-id]]
- [[poll GET until DONE]]
