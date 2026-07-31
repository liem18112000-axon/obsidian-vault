---
ai_hash: f35e86bce4d40075
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-13
entities: []
source: session 2026-07-13 eArchive api_calls capture
status: seedling
tags:
- luz-docs
- luz-jsonstore
- earchive
- logging
- gotcha
- jsf
title: Where to find a luz-docs API request body driven from the eArchive UI
type: lesson
---

# Where to find a luz-docs API request body driven from the eArchive UI

**The browser is never a source.** The Klara eArchive page is server-rendered JSF / xpertivy (`luz_epost_business_web`); the browser only posts JSF partial-submits to `dev.klara.tech/luz/faces/…`. The real chain **luz-webclient → luz-docs-view-controller → luz-docs → luz-jsonstore** runs server-side, so `browser_network_requests` never shows a `luz_docs/api` call.

Log sources, in the order to try them:

1. **`DocumentResource` inbound payload log** — only for `documents/search` and `documents/count`: `LOGGER.info("[search] tenant: %s - query payload: %s")` (and the `[count]` equivalent). This is the raw body in luz-docs' own query DSL (`{"query":…}` / `{"facets":…}`), *before* Mongo translation. Grep container logs for `DocumentResource` + `query payload:`.
2. **`JsonStoreLoggingFilter` downstream body** — `request-body=<mongo query>` that luz-docs sends to luz-jsonstore, i.e. the *translated/effective* query. Use it for endpoints with no resource-level payload log (e.g. `folders/search`), or to see the Mongo form. Read via [[luz-skill-flow-logs]] with `SERVICES=luz-docs,luz-jsonstore`.
3. **No body available:** `TriggerAsyncRequestFilter` logs only Method/URL/Referer/MediaType; `io.undertow.accesslog` gives `luz-uri=<METHOD> <path?query>` plus a following `status-code=` / `time-consuming=` (ms) line — good for URL + latency only.

**Correlation caveat:** Undertow recycles worker thread ids (`default task-N`) within a capture window, so a task-id + timestamp join is unreliable under concurrency — the distinct bodies are exact, but their 1:1 pairing to a specific `?query` variant may not be recoverable from logs alone.

**Parsing gotcha:** `gcloud logging read` output is multi-line YAML whose entries are separated by lines that are exactly `---`; parse `textPayload:` plus its 2-space-indented continuation lines rather than splitting naively.

## Related

- [[luz-skill-flow-logs]]
- [[luz-docs documentscount is ~130s on an 800k tenant — the 16-shard fan-out, not counting, is the bottleneck]]

%% ai-graph-start %%

**Related notes:**
- [[eArchive request flow and log correlation (perf)]]
- [[luz-docs documentscount is ~130s on an 800k tenant — the 16-shard fan-out, not counting, is the bottleneck]]
- [[01 Overview]]
- [[search-logic]]
- [[eArchive count baseline latency on dev ~80s for 128k docs (fan-out off)]]

%% ai-graph-end %%