---
title: "kind port remap: only hostPort binds the host; NodePort/targetPort stay internal"
created: 2026-08-06
type: lesson
status: seedling
source: "session 2026-08-06"
tags: [kubernetes, kind, ports, gotcha, leo-customer360]
---

# kind port remap: only hostPort binds the host; NodePort/targetPort stay internal

When remapping a **kind** (Kubernetes-in-Docker) local cluster's ports to avoid conflicts with ports already allocated on the host machine, only **one** port in the whole chain actually binds on the host: the `hostPort` in `kind/cluster.yaml` under `extraPortMappings`. Change that one (e.g. `8080 -> 18080`, `3000 -> 13000`).

Everything else in the port chain is internal to the cluster network and must **stay matched to what the container actually listens on** — do not touch them:

- `nodePort` (the `30xxx` value) — the port on the kind node; `extraPortMappings` maps `hostPort -> containerPort` where `containerPort` here is the nodePort.
- Service `port` and `targetPort`, and the pod `containerPort` — internal cluster networking; `targetPort`/`containerPort` must equal the app's real listen port.

The kind chain is: `host:hostPort  ->  node:nodePort(30xxx)  ->  Service:port  ->  pod:targetPort/containerPort`.

**Second gotcha — browser-facing vs in-cluster config.** After moving the host port, any config value the *browser* uses must track the new host port (e.g. `FRONTEND_API_HOSTNAME=http://localhost:18008`). But config values resolved *inside* the cluster keep the internal service port and stay unchanged (e.g. `http://keycloak:8080`, `http://minio:9000`, `DAGSTER_GRAPHQL_PORT=3000`). Confusing the two is the classic break.

Context: `leo-customer360` `k8s/overlays/local` (kind). The base ConfigMap is shared, so a browser-facing change there is safe only because the `vks` overlay patches `FRONTEND_API_HOSTNAME` to its public URL.

## Related

- [[Kubernetes NodePort Service]]
- [[Browser-facing vs in-cluster URLs]]
