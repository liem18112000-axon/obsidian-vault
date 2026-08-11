---
ai_hash: 48afa8199e4a07eb
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-11
entities:
- Two-generation API
- HTTP transport
- Client per generation
- Single merged client
- Generation
- Auth header
- Retry/backoff
- Retry-After
- Client-side rate limiting
- JSON parsing
- Typed error mapping
- Secret-redacted logging
- Paths
- Parameter casing
- Service objects
- Accesstrade wrapper
- '`accesstrade_integration/api_services`'
- '`transport.py`'
- '`classic/` client'
- '`obs/` client'
- '`api.accesstrade.vn`'
- Flat paths
- Snake_case
- Region gateway
- Site-scoped paths
- CamelCase
- Integration trap
- Named-kwarg signature
- '`approval=`'
- '`price_from=`'
- '`campaign_types=`'
- API surface
- '`**extra_params` passthrough'
- '`client.request()` escape hatch'
- Doc drift
- Architecture pattern (shared transport, client per generation)
- Accesstrade has two API generations
- Affiliate API engineering best practices
- Accesstrade API Integration - MOC
- Wrap a two-generation API as one shared transport plus one client per generation
source: session 2026-06-11, accesstrade_integration repo
status: seedling
tags:
- accesstrade
- api-design
- architecture
- python
title: Wrap a two-generation API as one shared transport plus one client per generation
type: model
---

# Wrap a two-generation API as one shared transport plus one client per generation

**When an API ships two non-interchangeable generations, build ONE shared HTTP transport and ONE client per generation — never a single merged client.** The transport owns everything endpoint-agnostic (auth header, retry/backoff with Retry-After, client-side rate limiting, JSON parsing, typed error mapping, secret-redacted logging); each generation client owns only its paths, parameter casing, and service objects.

Applied in the Accesstrade wrapper (`accesstrade_integration/api_services`): `transport.py` is shared, `classic/` (api.accesstrade.vn, flat paths, snake_case) and `obs/` (region gateway, site-scoped paths, camelCase) are siblings that cannot cross-contaminate — which directly enforces the #1 integration trap documented in [[Accesstrade has two API generations]].

A second deliberate choice: every endpoint keeps its own **named-kwarg signature** (`approval=`, `price_from=`, `campaignTypes` as `campaign_types=`) instead of a generic param-forwarding factory. The repetition is structural, not logic: the signature *is* the documented API surface, so discoverability and doc-fidelity beat DRY here. A `**extra_params` passthrough on each method plus a raw `client.request()` escape hatch absorb doc drift without code changes.

## Related

- [[Accesstrade has two API generations]]
- [[Affiliate API engineering best practices]]
- [[Accesstrade API Integration - MOC]]

%% ai-graph-start %%

**Related notes:**
- [[Accesstrade has two API generations]]
- [[Affiliate API engineering best practices]]
- [[Extract shared use-case code into a sibling shared package, not a peer use case]]
- [[Accesstrade API Integration - MOC]]
- [[Idempotent link minting with content-hash cache keys]]

**Relations:**
- Two-generation API — *ships* — Generation
- Two-generation API — *recommends* — HTTP transport
- Two-generation API — *recommends* — Client per generation
- Two-generation API — *discourages* — Single merged client
- HTTP transport — *owns* — Auth header
- HTTP transport — *owns* — Retry/backoff
- Retry/backoff — *uses* — Retry-After
- HTTP transport — *owns* — Client-side rate limiting
- HTTP transport — *owns* — JSON parsing
- HTTP transport — *owns* — Typed error mapping
- HTTP transport — *owns* — Secret-redacted logging
- Client per generation — *owns* — Paths
- Client per generation — *owns* — Parameter casing
- Client per generation — *owns* — Service objects
- Architecture pattern (shared transport, client per generation) — *applied in* — Accesstrade wrapper
- Accesstrade wrapper — *is* — `accesstrade_integration/api_services`
- `accesstrade_integration/api_services` — *contains* — `transport.py`
- `accesstrade_integration/api_services` — *contains* — `classic/` client
- `accesstrade_integration/api_services` — *contains* — `obs/` client
- `transport.py` — *is* — shared
- `classic/` client — *targets* — `api.accesstrade.vn`
- `classic/` client — *uses* — Flat paths
- `classic/` client — *uses* — Snake_case
- `obs/` client — *targets* — Region gateway
- `obs/` client — *uses* — Site-scoped paths
- `obs/` client — *uses* — CamelCase
- `classic/` client — *are* — siblings
- `obs/` client — *are* — siblings
- `classic/` client — *cannot cross-contaminate* — `obs/` client
- Architecture pattern (shared transport, client per generation) — *enforces* — Integration trap
- Integration trap — *documented in* — Accesstrade has two API generations
- Endpoint — *keeps* — Named-kwarg signature
- Named-kwarg signature — *includes* — `approval=`
- Named-kwarg signature — *includes* — `price_from=`
- Named-kwarg signature — *includes* — `campaign_types=`
- Named-kwarg signature — *is* — API surface
- `**extra_params` passthrough — *absorbs* — Doc drift
- `client.request()` escape hatch — *absorbs* — Doc drift
- Wrap a two-generation API as one shared transport plus one client per generation — *is related to* — Accesstrade has two API generations
- Wrap a two-generation API as one shared transport plus one client per generation — *is related to* — Affiliate API engineering best practices
- Wrap a two-generation API as one shared transport plus one client per generation — *is related to* — Accesstrade API Integration - MOC

%% ai-graph-end %%