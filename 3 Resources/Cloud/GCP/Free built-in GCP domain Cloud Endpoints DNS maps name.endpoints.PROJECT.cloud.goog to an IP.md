---
ai_hash: 5b212d55fedc7ae2
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-04
entities: []
source: session 2026-07-04, vinnstack public domain
status: seedling
tags:
- gcp
- dns
- gke
- cloud-endpoints
title: 'Free built-in GCP domain: Cloud Endpoints DNS maps name.endpoints.PROJECT.cloud.goog
  to an IP'
type: howto
---

# Free built-in GCP domain: Cloud Endpoints DNS maps name.endpoints.PROJECT.cloud.goog to an IP

GCP gives every project a free, Google-managed DNS namespace: `<name>.endpoints.<project-id>.cloud.goog`. To point one at any IP (e.g. a GKE LoadBalancer), deploy an EMPTY Cloud Endpoints service config - no domain purchase, no DNS zone:

    swagger: "2.0"
    info: { title: X DNS, version: "1.0.0" }
    host: name.endpoints.PROJECT.cloud.goog
    x-google-endpoints:
      - name: name.endpoints.PROJECT.cloud.goog
        target: 34.65.63.239
    paths: {}

    gcloud endpoints services deploy file.yaml --project=PROJECT

Prereqs/caveats:
- Enable servicemanagement/endpoints APIs on first use.
- PROMOTE the LB's ephemeral IP to a static address first (`gcloud compute addresses create NAME --addresses=IP --region=REGION`) or the record dangles when the service is recreated.
- This is DNS ONLY - traffic is unchanged (still plain HTTP unless you add an Ingress + ManagedCertificate). Google OAuth redirect URIs require HTTPS for non-localhost hosts, so login flows need the cert step too.
- The name also works as the domain for a GKE ManagedCertificate (it resolves to your LB, which is exactly what cert provisioning validates).

## Related

- [[Diagnose GCP console permission errors with the testIamPermissions REST probe]]

%% ai-graph-start %%

**Related notes:**
- [[GKE managed-cert HTTPS global IP, DNS before cert, NEG service, FrontendConfig redirect]]
- [[GKE LoadBalancer Service External vs Internal and how to tell by IP]]
- [[Diagnose GCP console permission errors with the testIamPermissions REST probe]]

%% ai-graph-end %%