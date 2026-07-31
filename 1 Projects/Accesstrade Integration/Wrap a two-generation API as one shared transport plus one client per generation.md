---
title: "Wrap a two-generation API as one shared transport plus one client per generation"
created: 2026-06-11
type: model
status: seedling
source: "session 2026-06-11, accesstrade_integration repo"
tags: [accesstrade, api-design, architecture, python]
---

# Wrap a two-generation API as one shared transport plus one client per generation

**When an API ships two non-interchangeable generations, build ONE shared HTTP transport and ONE client per generation — never a single merged client.** The transport owns everything endpoint-agnostic (auth header, retry/backoff with Retry-After, client-side rate limiting, JSON parsing, typed error mapping, secret-redacted logging); each generation client owns only its paths, parameter casing, and service objects.

Applied in the Accesstrade wrapper (`accesstrade_integration/api_services`): `transport.py` is shared, `classic/` (api.accesstrade.vn, flat paths, snake_case) and `obs/` (region gateway, site-scoped paths, camelCase) are siblings that cannot cross-contaminate — which directly enforces the #1 integration trap documented in [[Accesstrade has two API generations]].

A second deliberate choice: every endpoint keeps its own **named-kwarg signature** (`approval=`, `price_from=`, `campaignTypes` as `campaign_types=`) instead of a generic param-forwarding factory. The repetition is structural, not logic: the signature *is* the documented API surface, so discoverability and doc-fidelity beat DRY here. A `**extra_params` passthrough on each method plus a raw `client.request()` escape hatch absorb doc drift without code changes.

## Related

- [[Accesstrade has two API generations]]
- [[Affiliate API engineering best practices]]
- [[Accesstrade API Integration - MOC]]
