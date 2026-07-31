---
title: "Verify kubectl context before GKE rollout - _context file can disagree"
created: 2026-07-21
type: lesson
status: seedling
source: "session 2026-07-21 dev ship"
tags: [gke, kubectl, rollout, gotcha, luz-docs]
---

# Verify kubectl context before GKE rollout - _context file can disagree

The skills' `~/.claude/skills/_context` file (env selector) and the active `kubectl` context are independent state — they CAN disagree. Confirmed live 2026-07-21: `_context` said `dev` but `kubectl config current-context` was still `gke_klara-performance_...` from an earlier perf session; a blind rollout would have targeted the wrong cluster (or failed on missing `dev` namespace).

Pre-rollout ritual for luz-docs:
1. `kubectl config current-context` — must match the intended cluster (`gke_klara-nonprod_...` for dev), else `gcloud container clusters get-credentials klara-nonprod --zone europe-west6-a --project klara-nonprod`.
2. Pin the image to the exact build sha (`kubectl -n dev set image statefulset/luz-docs luz-docs=...:<sha>`) instead of rollout-latest, which grabs the NEWEST GAR tag and can deploy a concurrent master build.
3. Verify after: read `.spec.template...image` and per-pod images (filter container by name — container[0] on the pod is the ASM istio sidecar, not the app).

## Related

- [[Materialize tenant allowlist removed - cascade unconditional]]
