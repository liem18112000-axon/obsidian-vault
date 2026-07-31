---
title: "kubectl rollout status timeout must cover cold image pull time"
created: 2026-07-03
type: lesson
status: seedling
source: "Vinnstack GKE deploy 2026-07-03"
tags: [kubernetes, gke, rollout, gotcha]
---

# kubectl rollout status timeout must cover cold image pull time

`kubectl rollout status --timeout=<t>` counts the time a pod spends pulling its image, and a large image on a **cold** node (never pulled there before) can dominate that budget. A ~600 MB Next.js app image observed taking ~2m45s to pull on a fresh GKE node — so a `--timeout=300s` spuriously reports `error: timed out waiting for the condition` even though the rollout completes successfully moments later.

**Lesson:** size the rollout timeout for the *cold-pull* case, not the warm one. 600s is a safe default for a ~600 MB image. A timeout is not the same as a failure — always inspect pod events (`kubectl describe pod`) before concluding a deploy broke; look for `Pulling` → `Pulled ... in Xm` to confirm it was just image-pull latency.

Warm re-deploys to the same node (image cached) finish in seconds, which is why this only bites the first deploy to a given node.

## Related

- [[GKE Immediate-binding StorageClass deadlocks a single-replica StatefulSet across zones]]
