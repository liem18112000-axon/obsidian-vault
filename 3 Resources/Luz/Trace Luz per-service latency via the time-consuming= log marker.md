---
title: "Trace Luz per-service latency via the time-consuming= log marker"
created: 2026-08-10
type: howto
status: seedling
source: "session 2026-08-10 (luz-docs-import-api-test skill)"
tags: [luz, latency, logging, gcloud, tracing, gotcha]
---

# Trace Luz per-service latency via the time-consuming= log marker

Every Luz JVM service logs `<METHOD> Rest client response uri: <uri> time-consuming=<ms>` for **each outbound REST-client call**, via a JAX-RS `@Provider ClientResponseFilter` (in luz-docs-import it is `ch.klara.luz.docsimport.filter.RestClientResponseFilter`; sibling services carry their own). So a single service's logs reveal the latency of every hop it makes — e.g. luz-docs-import alone shows its calls to luz-antivirus (`/scanner`, the whole-zip scan + each per-file scan), luz-docs-view-controller, and luz-jsonstore.

**Why it matters:** you can build a per-service latency breakdown for a whole request chain without distributed tracing — just grep the `time-consuming=` marker.

**Technique** (used by the `luz-docs-import-api-test` skill's `fetch_timings.sh`):
1. `gcloud logging read` across the chain containers {luz-docs-import, luz-docs-view-controller, luz-docs, luz-antivirus, luz-jsonstore} with `textPayload:"time-consuming"` and a freshness window covering the run.
2. Extract ms with regex `time-consuming=(\d+)`; grab the endpoint from `uri:\s*(\S+)`.
3. Aggregate grouped by `container_name` + target, **collapsing embedded ids** (`/[0-9a-fA-F-]{8,}` → `/{id}`) so repeated calls to the same endpoint sum together.

**Gotcha:** antivirus scan time appears twice — once in luz-antivirus's own logs, and once as luz-docs-import's outbound `/scanner` client timing. Don't double-count when attributing wall-clock.

## Related

- [[Luz docs-import zip flow: upload-zip returns job-id]]
- [[poll GET until DONE]]
