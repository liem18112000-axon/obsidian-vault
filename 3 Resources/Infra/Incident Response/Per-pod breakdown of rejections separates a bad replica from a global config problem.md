---
ai_hash: a89a7a7b77eb189c
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-16
entities: []
source: session 2026-07-16 Klara Widget Store PROD incident
status: seedling
tags:
- incident-response
- kubernetes
- triage
- gke
title: Per-pod breakdown of rejections separates a bad replica from a global config
  problem
type: lesson
---

# Per-pod breakdown of rejections separates a bad replica from a global config problem

When a backend rejects a fraction of requests (e.g. HTTP 401, or a recurring auth exception like ApiKeyException), break the rejection count down **by destination pod** before deciding the fix:

- **Concentrated on one pod** (others clean) → that replica is bad (stale config/secret, corrupt cache, half-broken start). Fix = restart/replace that pod.
- **Uniform across all pods of the same replicaset** → not infrastructure; a **key / config / data** problem affecting the whole service (a rotated/expired credential, a bad shared config, specific tenants/keys). A pod restart will *not* help.

A partial failure (some requests succeed) is compatible with *both* shapes, so the per-pod distribution is what disambiguates them.

Concrete: Klara Widget Store outage — luztenant-service 401s / ApiKeyException were spread evenly across all 4 pods of replicaset `55c569d4d7` (~14-17 each), so it was a global key/config issue (a subset of API keys rejected service-wide), not a bad replica. Restarting pods would have been the wrong move.

Query shape: pull the access-log/error entries and `sort | uniq -c` on the destination pod label (plus status) to see the distribution instantly.

## Related

- [[Measure a feature outage's true blast radius by comparing any-severity vs error-severity user sets]]
- [[Klara PROD log access]]

%% ai-graph-start %%

**Related notes:**
- [[Measure a feature outage's true blast radius by comparing any-severity vs error-severity user sets]]
- [[Klara app API-key to token exchange flow (jwt-service to luztenant-service)]]
- [[Istio DC response_flag with round latency = caller read timeout]]
- [[klara-prod is a separate GCP project, not a namespace]]
- [[Log red herrings enclosing class name and baseline-noise lines]]

%% ai-graph-end %%