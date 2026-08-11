---
ai_hash: 8063a9510b58582d
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-22
entities:
- Evicting gate campaign-check keys from luz-cache clears L2 only
- gate campaign-check keys
- luz-cache
- L2 cache
- materialize/parallelize gate campaign-check results
- luz_docs_materialisation_campaign_check
- luz_docs_parallelize_campaign_check
- luz-skill-delete-cache
- DELETE /luz_cache/api/{tenant}/{key}
- api-forwarder
- admin all-tenant token
- gates
- DualCache
- in-JVM L1 cache
- campaign result
- TTL 3600s
- luz-docs pods
- stale gate verdict
- NotWritablePrimary via port-forward means forward targets a secondary
- eviction
- pod restart
- L1 expiration
source: session 2026-07-22
status: seedling
tags:
- luz-docs
- cache
- materialize
- gotcha
title: Evicting gate campaign-check keys from luz-cache clears L2 only
type: lesson
---

# Evicting gate campaign-check keys from luz-cache clears L2 only

The materialize/parallelize gate campaign-check results live in luz-cache under per-tenant keys:

- `luz_docs_materialisation_campaign_check`
- `luz_docs_parallelize_campaign_check`

Evict with the luz-skill-delete-cache skill (`DELETE /luz_cache/api/{tenant}/{key}` via api-forwarder, admin all-tenant token). BUT this clears only the L2 (luz-cache) layer — the gates use a DualCache with an in-JVM L1 (campaign result, TTL 3600s), so running luz-docs pods keep serving the stale gate verdict until L1 expires or the pod restarts. For an immediate effect, restart the pods after the evict.

## Related

- [[NotWritablePrimary via port-forward means forward targets a secondary]]

%% ai-graph-start %%

**Related notes:**
- [[Cache-epoch invalidation fails if the epoch is read through a local L1]]
- [[Campaign-gate template cache then campaign status L1 then repository L2]]
- [[DualCache L1 write ignores per-call TTL (uses domain default)]]
- [[Materialize gate cache never latches, hammers campaign service on every count]]
- [[Campaign COMPLETED status is only trusted after re-verifying document state (truth-check gate)]]

**Relations:**
- gate campaign-check keys — *are stored in* — luz-cache
- luz-cache — *is a* — L2 cache
- materialize/parallelize gate campaign-check results — *live in* — luz-cache
- luz-cache — *stores key* — luz_docs_materialisation_campaign_check
- luz-cache — *stores key* — luz_docs_parallelize_campaign_check
- luz-skill-delete-cache — *evicts* — gate campaign-check keys
- luz-skill-delete-cache — *uses API* — DELETE /luz_cache/api/{tenant}/{key}
- DELETE /luz_cache/api/{tenant}/{key} — *accessed via* — api-forwarder
- DELETE /luz_cache/api/{tenant}/{key} — *requires* — admin all-tenant token
- luz-skill-delete-cache — *clears only* — L2 cache
- gates — *use* — DualCache
- DualCache — *includes* — in-JVM L1 cache
- in-JVM L1 cache — *stores* — campaign result
- campaign result — *has* — TTL 3600s
- luz-docs pods — *serve* — stale gate verdict
- stale gate verdict — *persists until* — L1 expiration
- stale gate verdict — *persists until* — pod restart
- eviction — *requires* — pod restart
- Evicting gate campaign-check keys from luz-cache clears L2 only — *related to* — NotWritablePrimary via port-forward means forward targets a secondary

%% ai-graph-end %%