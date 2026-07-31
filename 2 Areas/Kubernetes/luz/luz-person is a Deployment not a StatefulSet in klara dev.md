---
title: "luz-person is a Deployment not a StatefulSet in klara dev"
created: 2026-06-22
type: observation
status: seedling
source: "session 2026-06-22"
tags: [kubernetes, gke, luz-person, gotcha]
---

# luz-person is a Deployment not a StatefulSet in klara dev

In the klara dev GKE cluster (`gke_klara-nonprod_europe-west6-a_klara-nonprod`, namespace `dev`), the `luz-person` workload is a Kubernetes **Deployment**, not a StatefulSet — unlike `luz-docs` and `luz-jsonstore` which are StatefulSets.

**Why it matters:** any tooling that hard-codes `statefulset/<name>` (e.g. `kubectl set image statefulset/...`, `kubectl rollout status statefulset/...`) fails on `luz-person` with `statefulsets.apps "luz-person" not found`. Detect kind by probing `kubectl get statefulset/<name>` then falling back to `kubectl get deployment/<name>`.

See [[rollout-latest skill auto-detects StatefulSet vs Deployment]].

## Related

- [[rollout-latest skill auto-detects StatefulSet vs Deployment]]
