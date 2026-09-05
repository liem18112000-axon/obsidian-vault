---
title: "luz-store on dev is a Deployment, not a StatefulSet — roll out with kubectl set image"
created: 2026-08-04
type: howto
status: seedling
source: "session 2026-08-04"
tags: [luz_store, kubernetes, deployment, rollout, gke, dev]
---

# luz-store on dev is a Deployment, not a StatefulSet — roll out with kubectl set image

On **dev**, the `luz-store` workload is a **Deployment**, not a StatefulSet. Tell by the pod names — ReplicaSet-hashed (`luz-store-66d75c9d7c-vfp7m`, `luz-store-85589bbd66-kxwtv`), not ordinal (`luz-store-0`). `kubectl get statefulset luz-store -n dev` returns `NotFound`.

## Consequence
The `google-skill-rollout-latest` skill targets **StatefulSets**, so it does NOT apply to luz-store. Roll out with a direct `kubectl set image` on the Deployment instead:

```bash
kubectl set image deployment/luz-store \
  luz-store=europe-west6-docker.pkg.dev/klara-repo/artifact-registry-container-images/luz-store:<FULL_COMMIT_SHA> \
  -n dev
kubectl rollout status deployment/luz-store -n dev --timeout=180s
```

## Details that matter
- Container name inside the pod = `luz-store` (the deployment spec has 1 container; pods show `2/2` because of an injected sidecar not in the spec).
- Image repo: `europe-west6-docker.pkg.dev/klara-repo/artifact-registry-container-images/luz-store`.
- **Image tags are the full git commit SHA** (40 hex), e.g. `8dfee39a7b7a8423bd045b89e23c354b5a221088`. Cloud Build pushes one tag per built commit. Confirm a tag exists before rolling: `gcloud artifacts docker images list <repo>/luz-store --include-tags --filter="tags:<SHA>"`.
- To roll out "the current branch image", use `git rev-parse HEAD` as the tag (not "latest" — latest may be someone else`s newer push).
- Requires kubectl context `gke_klara-nonprod_europe-west6-a_klara-nonprod` (see [[postgres skill dev port-forward needs the klara-nonprod GKE context, not kind-customer360]]).

## Related

- [[postgres skill dev port-forward needs the klara-nonprod GKE context]]
- [[not kind-customer360]]
