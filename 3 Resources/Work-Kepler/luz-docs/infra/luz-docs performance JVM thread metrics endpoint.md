---
title: luz-docs performance JVM thread metrics endpoint
tags: [luz-docs, performance, metrics, prometheus, wildfly]
created: 2026-07-16
---

# luz-docs performance JVM thread metrics endpoint

On the **klara-performance** cluster (`gke_klara-performance_europe-west6-a_klara-performance`, ns `performance`), the `luz-docs` StatefulSet runs a `metrics-proxy` sidecar (`nginx-metrics-proxy`) exposing **MicroProfile Metrics** on container port **9090** (named `metrics`).

## Gotcha
Metrics are **WildFly MicroProfile**, NOT Micrometer/Spring. So there is **no `jvm_threads_*`** family. Thread count lives under `base_*`:

- `base_thread_count` — live thread count
- `base_thread_daemon_count` — daemon threads
- `base_thread_max_count` — peak threads since start
- `application_luz_docs_worker_thread_usage_percentage` — app worker pool utilisation
- `wildfly_batch_jberet_*`, `wildfly_datasources_pool_*` — pool-level gauges

## How to read
```
POD=$(kubectl --context gke_klara-performance_europe-west6-a_klara-performance -n performance \
  get pods -l app=luz-docs -o jsonpath='{.items[0].metadata.name}')
kubectl --context ... -n performance port-forward $POD 19090:9090 &
curl -s localhost:19090/metrics | grep -E '^base_thread|worker_thread_usage'
```

~408 metric lines total. luz-docs StatefulSet = 5 replicas on performance. view-controller/webclient are Deployments (no metrics-proxy sidecar — thread metrics may be absent there).
