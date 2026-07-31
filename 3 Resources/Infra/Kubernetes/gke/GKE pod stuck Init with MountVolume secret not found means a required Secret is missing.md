---
title: "GKE pod stuck Init with MountVolume secret not found means a required Secret is missing"
created: 2026-07-03
type: lesson
status: seedling
source: "session 2026-07-03"
tags: [gke, kubernetes, secrets, rollout, gotcha]
---

# GKE pod stuck Init with MountVolume secret not found means a required Secret is missing

A GKE pod stuck in `Init:0/1` (or PodInitializing) with a repeated event `MountVolume.SetUp failed for volume "X" : secret "Y" not found` means the pod spec mounts a Secret that does not exist in the namespace. The pod never becomes Ready, so a StatefulSet/Deployment rollout waiting on it eventually `error: timed out waiting for the condition`.

Fix: create the missing Secret (e.g. `kubectl -n <ns> create secret generic Y --from-file=key.json=<file>`). kubelet retries the failed mount automatically (~every 2 min) so the pod can recover on its own; `kubectl -n <ns> rollout restart <kind>/<name>` forces it immediately. Vinnstack case: manifest mounted `vinnstack-gcp-key` (a GSA JSON key for key-based Google auth, in lieu of Workload Identity) that was documented as create-out-of-band but never created in dev.

## Related
[[3 Resources/Infra/Kubernetes/gke/Cloud Build GKE deploy get-credentials needs --project for a cross-project cluster]]

## Related

- [[3 Resources/Infra/Kubernetes/gke/Cloud Build GKE deploy get-credentials needs --project for a cross-project cluster]]
