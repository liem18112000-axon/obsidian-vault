---
title: "rollout-latest skill auto-detects StatefulSet vs Deployment"
created: 2026-06-22
type: howto
status: seedling
source: "session 2026-06-22"
tags: [kubernetes, kubectl, rollout, skill]
---

# rollout-latest skill auto-detects StatefulSet vs Deployment

The `google-skill-rollout-latest` skill (`rollout_latest.sh`) was originally hard-coded for StatefulSets. It now **auto-detects workload kind**: probe `kubectl get statefulset/<name>`, fall back to `kubectl get deployment/<name>`, build a `<kind>/<name>` ref, and use that everywhere. Overridable via `WORKLOAD_KIND=deployment|statefulset`.

**Why this is safe:** the rollout mechanics — `kubectl set image`, `rollout restart`, `rollout status`, and the `kubectl.kubernetes.io/last-applied-configuration` annotation sync — are identical for Deployments and StatefulSets, so only the resource ref differs.

Prompted by [[luz-person is a Deployment not a StatefulSet in klara dev]].

## Related

- [[luz-person is a Deployment not a StatefulSet in klara dev]]
