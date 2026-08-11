---
ai_hash: efc9221fb591b406
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-03
entities: []
source: session 2026-07-03
status: seedling
tags:
- gke
- kubernetes
- secrets
- rollout
- gotcha
title: GKE pod stuck Init with MountVolume secret not found means a required Secret
  is missing
type: lesson
---

# GKE pod stuck Init with MountVolume secret not found means a required Secret is missing

A GKE pod stuck in `Init:0/1` (or PodInitializing) with a repeated event `MountVolume.SetUp failed for volume "X" : secret "Y" not found` means the pod spec mounts a Secret that does not exist in the namespace. The pod never becomes Ready, so a StatefulSet/Deployment rollout waiting on it eventually `error: timed out waiting for the condition`.

Fix: create the missing Secret (e.g. `kubectl -n <ns> create secret generic Y --from-file=key.json=<file>`). kubelet retries the failed mount automatically (~every 2 min) so the pod can recover on its own; `kubectl -n <ns> rollout restart <kind>/<name>` forces it immediately. Vinnstack case: manifest mounted `vinnstack-gcp-key` (a GSA JSON key for key-based Google auth, in lieu of Workload Identity) that was documented as create-out-of-band but never created in dev.

## Related
[[3 Resources/Infra/Kubernetes/gke/Cloud Build GKE deploy get-credentials needs --project for a cross-project cluster]]

## Related

- [[3 Resources/Infra/Kubernetes/gke/Cloud Build GKE deploy get-credentials needs --project for a cross-project cluster]]

%% ai-graph-start %%

**Related notes:**
- [[Cloud Build GKE deploy get-credentials needs --project for a cross-project cluster]]
- [[Non-WI GKE Google API auth mount a GSA key at the well-known ADC path]]
- [[GKE Immediate-binding StorageClass deadlocks a single-replica StatefulSet across zones]]
- [[Rollout restart uses the LIVE spec - a manifest edited only in git changes nothing]]
- [[Deploying a stateful single-tenant app to GKE with a Cloud SQL proxy sidecar]]

%% ai-graph-end %%