---
title: Luz performance env cluster topology
tags: [luz, performance, gke, mongodb, infra]
created: 2026-07-16
---

# Luz performance env cluster topology

`performance.klara.tech` is served from a **separate GKE cluster and GCP project**, not a namespace on klara-nonprod.

- **kube context**: `gke_klara-performance_europe-west6-a_klara-performance`
- **project**: klara-performance, region europe-west6-a
- **app namespace**: `performance`
- Login realm host: `login-performance.klara.tech` (Keycloak). Profile after login = LiemCompany (no chooser popup).

## Service specs (ns `performance`)
| Service | Kind | Replicas | CPU req/lim | Mem req/lim |
| --- | --- | --- | --- | --- |
| luz-docs | StatefulSet | 5 | 1 / 15 | 10Gi / 10Gi |
| luz-docs-view-controller | Deployment | 8 | 50m / 12 | 4Gi / 5Gi |
| luz-webclient | Deployment | 1 | 10m / 7 | 12Gi / 12Gi |
| luz-jsonstore | Deployment | 3 | 500m / 12 | 512Mi / 10Gi |

luz-docs also runs `luz-docs-batch` + `luz-docs-import` (separate workloads, own image tags) and a `metrics-proxy` sidecar (MicroProfile metrics on :9090 — see [[luz-docs performance JVM thread metrics endpoint]]).

## MongoDB (ns `performance-mongodb-clusters`)
- **16 per-tenant-shard replica sets** `luz-mongodb00-cluster-rs` … `0f` + `ff`, each **3-node** (3/3). Routing: first hex char of tenant id **mod 16** → cluster `0X` (dev uses mod 4, perf mod 16).
- Legacy sharded cluster still in ns `performance`: `luz-mongodb-cluster-{cfg,mongos,rs0}` (3 each).
- Engine: **Percona Server for MongoDB 4.4.16-16**.
- Per-mongod: CPU 250m/8, Mem 500M/16G, storage 64Gi (sc `luz-mongodb-balanced`).

## Rollout to a specific SHA (perf)
`kubectl set image` against the already-built tag in Artifact Registry (`klara-repo`) — no rebuild. luz-docs is a StatefulSet (partitioned rolling update, one pod at a time). Verify tag exists first: `gcloud artifacts docker images describe <repo>/<img>:<sha>`.

Related: [[Luz K count-partitions env var]]
