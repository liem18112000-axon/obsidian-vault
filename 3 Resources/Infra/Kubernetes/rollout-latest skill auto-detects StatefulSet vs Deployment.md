---
ai_hash: 2efd7b343c716fef
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-22
entities: []
source: session 2026-06-22
status: seedling
tags:
- kubernetes
- kubectl
- rollout
- skill
title: rollout-latest skill auto-detects StatefulSet vs Deployment
type: howto
---

# rollout-latest skill auto-detects StatefulSet vs Deployment

The `google-skill-rollout-latest` skill (`rollout_latest.sh`) was originally hard-coded for StatefulSets. It now **auto-detects workload kind**: probe `kubectl get statefulset/<name>`, fall back to `kubectl get deployment/<name>`, build a `<kind>/<name>` ref, and use that everywhere. Overridable via `WORKLOAD_KIND=deployment|statefulset`.

**Why this is safe:** the rollout mechanics — `kubectl set image`, `rollout restart`, `rollout status`, and the `kubectl.kubernetes.io/last-applied-configuration` annotation sync — are identical for Deployments and StatefulSets, so only the resource ref differs.

Prompted by [[luz-person is a Deployment not a StatefulSet in klara dev]].

## Related

- [[luz-person is a Deployment not a StatefulSet in klara dev]]

%% ai-graph-start %%

**Related notes:**
- [[luz-person is a Deployment not a StatefulSet in klara dev]]
- [[Shipping luz_docs_statistic trigger is docs-statistic-service and dev runs a Deployment, not a StatefulSet]]
- [[Verify kubectl context before GKE rollout - _context file can disagree]]
- [[luz-docs Cloud Build pushes an image for every branch but only master updates luz_kubernetes]]
- [[klara-prod is a separate GCP project, not a namespace]]

%% ai-graph-end %%