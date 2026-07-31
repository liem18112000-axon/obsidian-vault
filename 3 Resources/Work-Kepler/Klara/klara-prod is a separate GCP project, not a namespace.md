---
title: "klara-prod is a separate GCP project, not a namespace"
created: 2026-06-30
type: lesson
status: seedling
source: "PROD jwt-service investigation 2026-06-30; luz_docs prod migration incident 2026-07-10"
tags: [klara, gcp, gke, gcloud, logging, prod, access, gotcha]
---

# klara-prod is a separate GCP project, not a namespace

PROD lives in its **own GCP project and its own GKE cluster** — `project=klara-prod`, `cluster_name=klara-prod`, `namespace_name=prod`, `location=europe-west6-a`. It is not a namespace variant of klara-nonprod. Sibling projects: klara-nonprod (dev/test/dev-staging), klara-performance, klara-infra, klara-repo.

**Skill defaults silently miss it.** `google-skill-gke-logs` / `gke-monitor` default to `CLUSTER_PROJECT=klara-nonprod CLUSTER_NAME=klara-nonprod`, which returns **zero results — not an error** — when aimed at prod. Override explicitly:

```
CLUSTER_PROJECT=klara-prod CLUSTER_NAME=klara-prod NAMESPACE=prod
```

Confirmed 2026-07-10: an exact log line found nothing on klara-nonprod and matched immediately on klara-prod.

**Logging read and cluster access are separate grants.** The everyday account (`lam.nguyen@axonactive.com`) has Cloud Logging read on klara-prod but is denied `container.clusters.list` / `run.services.list` (403), and no kubectl context for klara-prod exists locally. So a PROD investigation must run **entirely through `gcloud logging read`**: no `kubectl get pods`, no port-forward, no direct prod Mongo. Discover pod names, replica counts and restarts *from the logs themselves* (distinct `resource.labels.pod_name`, the k8s `events` logName).

## Related

- [[Istio DC response_flag with round latency = caller read timeout]]
- [[Klara app API-key to token exchange flow (jwt-service to luztenant-service)]]
