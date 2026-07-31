---
ai_hash: 534c689c7e20e2d0
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-11
entities: []
source: session 2026-06-11, accesstrade_integration repo
status: seedling
tags:
- affiliate
- accesstrade
- idempotency
- caching
- python
title: Idempotent link minting with content-hash cache keys
type: howto
---

# Idempotent link minting with content-hash cache keys

**Make write-endpoints idempotent by keying a cache on a content hash of every input that influences the output, with None-valued params dropped before hashing so explicit-None equals absent.** For affiliate link minting: `sha256(json.dumps({campaign_id, url, non-null tracking params}, sort_keys=True))` — the same (campaign, origin URL, subs/utm) always yields the same affiliate link, so cache hits skip the HTTP call entirely and only misses are batched into one request.

Two defensive details from `api_services/classic/links.py`:
- **Origin matching**: the API's success entries *should* carry an origin-URL field, but the field name is undocumented — try a list of candidate keys, and when none exist fall back to positional zip only if `len(entries) == len(requested_urls)`. Unmatched entries are still returned to the caller, just not cached.
- **Sort the params** in the canonical JSON so dict insertion order can never split the cache.

This implements the idempotent-writes practice from [[Affiliate API engineering best practices]] for [[Accesstrade tracking link creation]].

## Related

- [[Accesstrade tracking link creation]]
- [[Affiliate API engineering best practices]]
- [[Accesstrade SubID attribution]]

%% ai-graph-start %%

**Related notes:**
- [[Affiliate API engineering best practices]]
- [[Accesstrade tracking link creation]]
- [[Use case - bulk tracking link generation]]
- [[An optimization-only cache should fail soft, never raise on backend errors]]
- [[Affiliate content-brief generator produces the grounded skeleton, not the prose]]

%% ai-graph-end %%