---
title: "namespaces dev not found on port-forward means wrong kubectl context"
created: 2026-07-24
type: lesson
status: seedling
source: "session 2026-07-24"
tags: [kubectl, port-forward, gke, klara, gotcha]
---

# namespaces dev not found on port-forward means wrong kubectl context

kubectl port-forward failing with 'Error from server (NotFound): namespaces "dev" not found' means the CURRENT CONTEXT points at a cluster without that namespace — check kubectl config current-context before debugging the service. In this environment the dev namespace lives on gke_klara-nonprod_europe-west6-a_klara-nonprod; the performance cluster context (gke_klara-performance_europe-west6-a_klara-performance) has no dev namespace and is easy to be left on after perf work.

Verified: service/luz-alloydb-main in dev is a selector-backed internal-LoadBalancer service (app=luz-alloydb-main, ILB 192.168.1.171, 2 pod endpoints) — genuinely port-forwardable, so failures are context/local-port issues, not the service. Prefer a one-off --context flag over use-context to avoid clobbering the session's active cluster, and avoid --address 0.0.0.0 (exposes the DB forward to the LAN) unless required.

## Related
- [[Atlassian MCP grant is per-site - axonivy.atlassian.net not covered]]
