---
title: "Kustomize component makes a service tier optional per environment"
created: 2026-08-03
type: lesson
status: seedling
source: "session 2026-08-03"
tags: [kubernetes, kustomize, component, overlays, technique]
---

# Kustomize component makes a service tier optional per environment

To run the same app against different backing services per environment (in-cluster data locally, managed/cloud data in prod), put the swappable tier in a **Kustomize Component**, not in `base`.

Pattern: `base/` holds only the env-agnostic app tier. A `components/in-cluster-data/` dir (with `kind: Component`, `apiVersion: kustomize.config.k8s.io/v1alpha1`) holds Postgres/Redis/Kafka/MinIO. The `local` overlay lists it under `components:` so those StatefulSets render; the `prod`/`vks` overlay omits it and instead patches the app's ConfigMap to point at managed endpoints. This is cleaner than putting data services in base and deleting them per-overlay with fragile `$patch: delete` directives.

Supporting tricks used together:
- Per-overlay `secretGenerator` reading a gitignored `secret.env`, with `generatorOptions.disableNameSuffixHash: true` so `base` can reference the Secret by a stable name (`c360-secrets`) without a hash suffix.
- `images:` transformer in each overlay to swap `:local` (kind-loaded) for registry images.
- Verify any overlay renders with `kubectl kustomize overlays/<env>` before applying.

## Related
- [[Customer360 Kubernetes deployment (local kind + GreenNode VKS)]]
