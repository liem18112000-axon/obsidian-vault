---
title: "Customer360 Kubernetes deployment (local kind + GreenNode VKS)"
created: 2026-08-03
type: howto
status: seedling
source: "session 2026-08-03"
tags: [kubernetes, kustomize, kind, vks, customer360, local-dev]
---

# Customer360 Kubernetes deployment (local kind + GreenNode VKS)

`leo-customer360/k8s/` deploys the whole CDP stack on Kubernetes with Kustomize (base + components + overlays), mirroring the `terraform/` env pattern. Built local-first (kind) with a VKS overlay for GreenNode.

**Structure:** `base/` = app tier (api, frontend, dagster, cir worker, keycloak + db-init job, shared ConfigMap `c360-config`, namespace). `components/in-cluster-data/` = postgres, redis, kafka (single-node KRaft), minio + seed jobs. `overlays/local` = base + the data component + NodePorts + `:local` images; `overlays/vks` = base + Ingress + registry images + managed endpoints, and it OMITS the data component (app points at the managed vDB + vStorage from `terraform/`).

**Single-click:** `k8s/scripts/up.sh` = create kind cluster → build+load images → `kubectl apply -k overlays/local` → wait → print URLs (api :8008, frontend :8890, keycloak :8080, dagster :3000, minio :9000/:9001). `down.sh` deletes the cluster.

**Notable:** `backend-system` had NO Dockerfile — one was added to run `dagster dev` with the 7 workspace code locations (DAGSTER_HOME on a PVC). Secrets come from a per-overlay `secretGenerator` reading `secret.env` (gitignored, `disableNameSuffixHash: true` so base can reference `c360-secrets` by a stable name). Local runs with `SSO_LOGIN=false` (header auth) since there is no automated Keycloak realm seed.

## Related
- [[Kustomize component makes a service tier optional per environment]]
- [[LEO Customer360 GreenNode Terraform infrastructure]]
- [[Customer360 GreenNode region split: compute HCM03, vStorage HCM04]]
