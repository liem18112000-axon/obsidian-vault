---
ai_hash: ecca3a8305713a68
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-22
entities: []
source: session 2026-06-22
status: seedling
tags:
- kubernetes
- gke
- luz-person
- gotcha
title: luz-person is a Deployment not a StatefulSet in klara dev
type: observation
---

# luz-person is a Deployment not a StatefulSet in klara dev

In the klara dev GKE cluster (`gke_klara-nonprod_europe-west6-a_klara-nonprod`, namespace `dev`), the `luz-person` workload is a Kubernetes **Deployment**, not a StatefulSet — unlike `luz-docs` and `luz-jsonstore` which are StatefulSets.

**Why it matters:** any tooling that hard-codes `statefulset/<name>` (e.g. `kubectl set image statefulset/...`, `kubectl rollout status statefulset/...`) fails on `luz-person` with `statefulsets.apps "luz-person" not found`. Detect kind by probing `kubectl get statefulset/<name>` then falling back to `kubectl get deployment/<name>`.

See [[rollout-latest skill auto-detects StatefulSet vs Deployment]].

## Related

- [[rollout-latest skill auto-detects StatefulSet vs Deployment]]

%% ai-graph-start %%

**Related notes:**
- [[rollout-latest skill auto-detects StatefulSet vs Deployment]]
- [[Shipping luz_docs_statistic trigger is docs-statistic-service and dev runs a Deployment, not a StatefulSet]]
- [[Verify kubectl context before GKE rollout - _context file can disagree]]
- [[klara-prod is a separate GCP project, not a namespace]]
- [[Luz performance env cluster topology]]

%% ai-graph-end %%