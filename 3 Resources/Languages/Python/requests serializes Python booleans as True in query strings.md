---
ai_hash: 56a4be8c844e669e
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-11
entities: []
source: session 2026-06-11, accesstrade_integration repo
status: seedling
tags:
- python
- requests
- gotcha
- http
title: requests serializes Python booleans as True in query strings
type: lesson
---

# requests serializes Python booleans as True in query strings

**Python requests serializes a boolean query param with str(), so `params={'flag': True}` hits the API as `flag=True` — many APIs (including Accesstrade) silently treat that as falsy or invalid because they expect lowercase `true`.** The failure is silent: no error, just wrong filtering.

Fix it once at the transport layer with a param cleaner that runs on every request, rather than per call site:
- `True/False` → `"true"/"false"`
- `None` → drop the key entirely (so optional params default cleanly)
- `list/tuple/set` → comma-join (the common multi-value convention; requests would otherwise repeat the key)
- `Enum` → its `.value`

Implemented as `_clean_params()` in `api_services/transport.py`; one place to audit, every endpoint inherits it.

## Related

- [[Affiliate API engineering best practices]]

%% ai-graph-start %%

**Related notes:**
- [[Wrap a two-generation API as one shared transport plus one client per generation]]

%% ai-graph-end %%