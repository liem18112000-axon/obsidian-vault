---
title: "Idempotent link minting with content-hash cache keys"
created: 2026-06-11
type: howto
status: seedling
source: "session 2026-06-11, accesstrade_integration repo"
tags: [affiliate, accesstrade, idempotency, caching, python]
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
