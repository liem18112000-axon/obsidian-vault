---
title: "Evicting gate campaign-check keys from luz-cache clears L2 only"
created: 2026-07-22
type: lesson
status: seedling
source: "session 2026-07-22"
tags: [luz-docs, cache, materialize, gotcha]
---

# Evicting gate campaign-check keys from luz-cache clears L2 only

The materialize/parallelize gate campaign-check results live in luz-cache under per-tenant keys:

- `luz_docs_materialisation_campaign_check`
- `luz_docs_parallelize_campaign_check`

Evict with the luz-skill-delete-cache skill (`DELETE /luz_cache/api/{tenant}/{key}` via api-forwarder, admin all-tenant token). BUT this clears only the L2 (luz-cache) layer — the gates use a DualCache with an in-JVM L1 (campaign result, TTL 3600s), so running luz-docs pods keep serving the stale gate verdict until L1 expires or the pod restarts. For an immediate effect, restart the pods after the evict.

## Related

- [[NotWritablePrimary via port-forward means forward targets a secondary]]
