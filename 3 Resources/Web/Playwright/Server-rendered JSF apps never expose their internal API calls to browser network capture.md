---
ai_hash: bccda0a4796d2c75
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-23
entities: []
source: session 2026-07-23, luz-docs earchive-perf-trace tool
status: seedling
tags:
- playwright
- jsf
- testing
- gotcha
title: Server-rendered JSF apps never expose their internal API calls to browser network
  capture
type: lesson
---

# Server-rendered JSF apps never expose their internal API calls to browser network capture

When a page is server-rendered JSF/xpertivy (or any framework where the browser only POSTs partial-submits to a faces/view-state endpoint), Playwright's `page.on('request', ...)` network capture will NEVER see the backend's own internal REST API calls -- those happen entirely server-side, one service calling another (e.g. luz-webclient -> luz-docs-view-controller -> luz-docs -> luz-jsonstore), invisible to anything hooked into the browser's network stack.

Concrete case: tried to sniff the tenant id out of the eArchive page's network requests by matching `/luz_docs/api/{tenant}/` in `page.on('request')` -- it never fired, because the browser only ever calls `dev.klara.tech/luz/faces/...` (JSF partial-submit); the /luz_docs/api/ calls are made server-to-server. This was already explicitly documented in the very doc (docs/performance-test-800k/apis/raw_api_calls_dev.md) I'd read earlier the same session -- read a doc once is not the same as internalizing it when writing code later. Re-check assumptions against docs you've already read, don't just recall the gist.

Fix/pattern: for a JSF-style app, get context you need (tenant id, request id, etc.) from a static/known lookup keyed by env+account instead of runtime network sniffing, or by correlating server-side logs after the fact (grep GKE logs for the account/session, not by capturing it client-side).

## Related

- [[Playwright has no way to merge N trials into one browsable trace.zip]]

%% ai-graph-start %%

**Related notes:**
- [[luz-docs API request bodies are only observable as downstream luz-jsonstore queries]]
- [[JSF LetterStorageDetail instance URL dies mid-benchmark; remint via eArchive menu]]
- [[eArchive perf test plan 5 scenarios, all automated by trace tool]]
- [[Timing PrimeFaces dialog opens trusted click + stale-guard the reused dialog node]]
- [[Playwright multi-trial trace merging via tracing chunks]]

%% ai-graph-end %%