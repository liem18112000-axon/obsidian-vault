---
ai_hash: 7d08b86c41ec1658
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-03
entities: []
source: Vinnstack GKE deploy 2026-07-03
status: seedling
tags:
- gke
- workload-identity
- gcp
- iam
- gotcha
title: Creating the GSA a KSA annotation references activates WI routing and can break
  a pod
type: lesson
---

# Creating the GSA a KSA annotation references activates WI routing and can break a pod

On a GKE cluster with Workload Identity enabled, creating the Google service account (GSA) that a Kubernetes SA's `iam.gke.io/gcp-service-account` annotation *references* can silently BREAK a running pod. Before the GSA exists the annotation is a dangling reference; the moment the GSA exists, the GKE metadata server starts routing the pod's token requests (`service-accounts/default/token`) to that GSA — and if there is no `roles/iam.workloadIdentityUser` binding letting the KSA impersonate it, every token request fails with `metadata ... token ... not defined`. Symptom: sidecars that were working (e.g. Cloud SQL Auth Proxy) suddenly cannot authenticate.

**Lesson:** WI is all-or-nothing — annotation + GSA + workloadIdentityUser binding must all be present together. Adding the GSA without the binding is worse than having no GSA. On a WI-enabled cluster the node-SA fallback is also disabled, so a pod with a dangling/incomplete WI setup gets NO Google credentials at all.

## Related

- [[3 Resources/Infra/Kubernetes/GKE LoadBalancer Service External vs Internal and how to tell by IP]]

%% ai-graph-start %%

**Related notes:**
- [[Non-WI GKE Google API auth mount a GSA key at the well-known ADC path]]
- [[Cloud SQL Auth Proxy needs roles-cloudsql.client on the connecting identity or it 403s NOT_AUTHORIZED]]
- [[Deploying a stateful single-tenant app to GKE with a Cloud SQL proxy sidecar]]
- [[GKE managed-cert HTTPS global IP, DNS before cert, NEG service, FrontendConfig redirect]]
- [[Set up GitHub Actions to GCP via Workload Identity Federation]]

%% ai-graph-end %%