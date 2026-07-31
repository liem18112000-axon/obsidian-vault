---
title: "luz-docs API request bodies are only observable as downstream luz-jsonstore queries"
created: 2026-07-13
type: lesson
status: seedling
source: "session 2026-07-13 eArchive api_calls capture"
tags: [luz-docs, earchive, logging, gotcha, jsf]
---

# luz-docs API request bodies are only observable as downstream luz-jsonstore queries

> **Correction (2026-07-13):** the original title/claim was too strong. luz-docs **does** log inbound
> bodies for `documents/search` and `documents/count`. The "only downstream" rule holds only for
> endpoints without a resource-level payload log (e.g. `folders/search`).

When you need the request body of a **luz-docs** API call driven from the Klara **eArchive** UI (dev):

- The eArchive page is a **server-rendered JSF / xpertivy** page (`luz_epost_business_web`). The browser only posts JSF partial-submits to `dev.klara.tech/luz/faces/...`; the real chain **luz-webclient → luz-docs-view-controller → luz-docs → luz-jsonstore** runs server-side. So `browser_network_requests` shows JSF POSTs, never `luz_docs/api` calls — the browser is never a source for luz-docs bodies.
- **`DocumentResource` DOES log the inbound body** for search/count: `LOGGER.info("[search] tenant: %s - query payload: %s")` and the `[count]` equivalent. The payload is the raw request body in luz-docs' own query DSL (`{"query":…}` / `{"facets":…}`), *before* translation to Mongo. Grep the luz-docs container logs for `DocumentResource` + `query payload:`.
- `TriggerAsyncRequestFilter` logs only `Method / URL / Referer / MediaType`, and `io.undertow.accesslog` gives the `luz-uri=<METHOD> <path?query>` line + a following `status-code=` + `time-consuming=` (ms) line — good for URL+query+latency, no body. So *some* endpoints (e.g. `folders/search`, which has no resource-level payload log) still leave no inbound body in the logs.
- `JsonStoreLoggingFilter` logs the **downstream** `request-body=<mongo query>` luz-docs sends to luz-jsonstore — the *translated/effective* body. Use this for endpoints whose inbound body isn't logged, or to see the Mongo form.

Practical order: check `DocumentResource … query payload:` first (true inbound body); fall back to the luz-jsonstore downstream `request-body=` (via [[luz-skill-flow-logs]], scope `SERVICES=luz-docs,luz-jsonstore`) when the resource doesn't log it. The browser is never useful here.

Caveat when correlating body → exact query-param variant: Undertow recycles worker thread-ids (`default task-N`) within a capture window, so a task-id + timestamp join is unreliable for high-concurrency bursts — the distinct bodies are exact, but their 1:1 pairing to a specific `?query` variant may not be recoverable from logs alone.

Gotcha corollary: log entries from `gcloud logging read` are multi-line YAML separated by lines that are exactly `---`; parse a `textPayload:` plus its 2-space-indented continuation lines, do not split naively.

## Related

- [[luz-skill-flow-logs]]
- [[eArchive documents/count fans out to 16 luz-jsonstore shard queries]]
