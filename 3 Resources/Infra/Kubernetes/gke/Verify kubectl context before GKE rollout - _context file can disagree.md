---
title: "Verify kubectl context before GKE rollout - _context file can disagree"
created: 2026-07-21
updated: 2026-07-31
type: lesson
status: seedling
source: "session 2026-07-21 dev ship; session 2026-07-24 port-forward"
tags: [gke, kubectl, rollout, port-forward, gotcha, klara, luz-docs]
---

# Verify kubectl context before GKE rollout - _context file can disagree

The skills' `~/.claude/skills/_context` file (env selector) and the active `kubectl` context are **independent state and CAN disagree**. Confirmed 2026-07-21: `_context` said `dev` while `kubectl config current-context` was still `gke_klara-performance_europe-west6-a_klara-performance` from an earlier perf session — a blind rollout would have hit the wrong cluster.

**Symptom of the same root cause on any command:** `Error from server (NotFound): namespaces "dev" not found` on `kubectl port-forward` / `get` means the current context points at a cluster with no `dev` namespace (the performance cluster) — check the context before debugging the service. In this environment `dev` lives on `gke_klara-nonprod_europe-west6-a_klara-nonprod`.

Pre-rollout ritual for luz-docs:
1. `kubectl config current-context` — must match the intended cluster, else `gcloud container clusters get-credentials klara-nonprod --zone europe-west6-a --project klara-nonprod`.
2. Pin the image to the exact build sha (`kubectl -n dev set image statefulset/luz-docs luz-docs=…:<sha>`) instead of rollout-latest, which grabs the NEWEST GAR tag and can deploy a concurrent master build.
3. Verify after: read `.spec.template…image` and per-pod images (filter container BY NAME — container[0] on the pod is the ASM istio sidecar, not the app).

Prefer a one-off `--context` flag over `use-context` so you do not clobber the session's active cluster. Avoid `--address 0.0.0.0` on a port-forward (exposes e.g. a DB tunnel to the LAN).

## Related

- [[Stale kubectl port-forward on a reused local port causes silent wrong-target auth failures]]
- [[Materialize tenant allowlist removed - cascade unconditional]]
- [[Atlassian MCP grant is per-site - axonivy.atlassian.net not covered]]
