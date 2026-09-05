---
title: "Jaeger Dependencies DAG is empty until a trace spans 2 services; force one with opentelemetry-instrument from inside a container"
created: 2026-08-23
type: lesson
status: seedling
source: "leo-customer360 uat, session 2026-08-23"
tags: [jaeger, opentelemetry, tracing, dag, leo-customer360]
---

# Jaeger Dependencies DAG is empty until a trace spans 2 services; force one with opentelemetry-instrument from inside a container

**Jaeger's "Dependencies" (DAG) page shows service->service edges, and an edge exists ONLY when a single trace spans >=2 services** via propagated context (a server making a traced outbound call to another instrumented server, passing W3C traceparent). Per-service traces alone create NO edges, so a fresh setup shows an empty DAG even though Search lists every service. Browser->service calls don't count (the browser starts a fresh trace); DB/Redis calls show as child SPANS, not DAG nodes (they aren't OTel "services").

**Empty DAG != broken.** Confirm with the API: `GET /jaeger/api/dependencies?endTs=<now_ms>&lookback=<ms>` -> `{"data":[]}` means no cross-service links yet.

**Force/prove a cross-service edge** from inside an OTel-auto-instrumented service container (its env has OTEL_SERVICE_NAME + OTEL_EXPORTER_OTLP_ENDPOINT + OTEL_SDK_DISABLED=false):
```
docker exec <serviceA> opentelemetry-instrument python -c \
  'import sys,urllib.request as u,contextlib
for _ in range(3):
    with contextlib.suppress(Exception): u.urlopen(sys.argv[1],timeout=6).read()' \
  http://127.0.0.1:<serviceB_port>/<path>
```
Key point: a plain `docker exec python ...` is NOT traced — the auto-instrumentation only loads when the process starts under `opentelemetry-instrument`. Running the one-off under `opentelemetry-instrument` loads the urllib/httpx instrumentor, creates a client span as serviceA, injects traceparent; serviceB (also instrumented) continues the same trace -> edge serviceA->serviceB. The short-lived process flushes spans via the SDK's atexit handler (even a 404 still records the span). A 404 target still works.

**Finding:** Jaeger all-in-one with **badger** storage DOES compute the DAG from stored spans (edges appeared within seconds of the cross-service traces, no spark-dependencies job). The spark-dependencies batch job is only needed for Elasticsearch/Cassandra backends.

Source: leo-customer360 uat (customer360-api -> ads-server / frontend-admin), 2026-08-23.
