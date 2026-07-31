---
title: "Approximating list-all over a search-only API: union broad seed queries, paginate, dedupe"
created: 2026-07-14
type: lesson
status: seedling
source: "session 2026-07-14"
tags: [api, search, mcp, pattern, gotcha, polaris]
---

# Approximating list-all over a search-only API: union broad seed queries, paginate, dedupe

When an API only offers SEARCH (requires a non-empty query, rejects empty, no list endpoint) but you need to show everything, approximate list-all by: **union a few broad seed queries + paginate each via the response's hasMore + dedupe by id**.

Concrete (Vinnstack Polaris skills tab, 2026-07-14): the Polaris MCP catalog is search-only. `listAll()` in lib/account/polarisCli.ts loops seeds `['e','a','i','o']` (common English letters that collectively match every item), pages while `hasMore`, and dedupes into a Map by id.

**Key gotcha — one broad query is NOT exhaustive.** A single `q='a'` returned 22 skills; the union of four vowel seeds returned **25**. Semantic/text search ranks + thresholds, so any single term misses items that term doesn't match well. Multiple diverse seeds close the gap. Verify the union count exceeds any single-seed count before trusting it.

Pattern pairs well with: load-all once on the client, then FILTER client-side (instant, no round-trips) rather than re-querying the server per keystroke. Keep a server `?q=` path too for callers that genuinely want ranked search (e.g. a compact picker). Related: [[Polaris MCP is search-only 5 tools, no list-all, driven over HTTP JSON-RPC not the CLI]].

## Related

- [[3 Resources/Work-Side/Polaris/Polaris MCP is search-only 5 tools, no list-all, driven over HTTP JSON-RPC not the CLI]]
