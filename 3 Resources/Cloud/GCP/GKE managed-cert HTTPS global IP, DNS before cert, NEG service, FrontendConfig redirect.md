---
title: "GKE managed-cert HTTPS: global IP, DNS before cert, NEG service, FrontendConfig redirect"
created: 2026-07-04
type: howto
status: seedling
source: "session 2026-07-04, vinnstack HTTPS"
tags: [gke, https, ingress, managed-certificate]
---

# GKE managed-cert HTTPS: global IP, DNS before cert, NEG service, FrontendConfig redirect

Recipe for free HTTPS on GKE with a Google-managed cert (works with a cloud.goog Endpoints hostname):

1. **Global static IP, not regional.** A gce-class Ingress (global external HTTPS LB) requires `gcloud compute addresses create NAME --global`; a regional address (what an L4 LoadBalancer Service uses) silently won't attach. If migrating from an L4 service, the domain must move to the NEW global IP.
2. **DNS first, cert second.** ManagedCertificate validates by resolving the domain to the LB - deploy the DNS record before (or together with) the Ingress; the cert sits `Provisioning` until resolution works, then takes 15-60 min to go `Active`. Watch: `kubectl describe managedcertificate NAME`.
3. **Backend service needs NEG**: annotate the ClusterIP service `cloud.google.com/neg: '{"ingress": true}'` for container-native LB; GKE derives the LB health check from the pod's readinessProbe.
4. **HTTP->HTTPS redirect** is a `FrontendConfig` (`redirectToHttps`) referenced from the Ingress annotation `networking.gke.io/v1beta1.FrontendConfig` - the LB serves the redirect, no app code.
5. Ingress annotations that tie it together: `kubernetes.io/ingress.global-static-ip-name`, `networking.gke.io/managed-certificates`.

Gotcha chain to expect: cert stuck Provisioning = DNS not resolving or resolving to the wrong (old/regional) IP.

**Observed second stall cause (2026-07-04):** adding the NEG annotation to an ALREADY-RUNNING ClusterIP service didn't take - the ingress controller kept failing translation for ~2h with `service is type "ClusterIP", expected "NodePort" or "LoadBalancer" when not using NEGs` (visible only in `kubectl describe ingress` events, x34). It unblocked when a pod ROLLOUT refreshed the service endpoints, which attached them to the NEG; the LB (UrlMap + both TargetProxies + both ForwardingRules) then built in ~1 min and the cert's FailedNotVisible cleared on the next validation retry. Rule: after annotating a live service with `cloud.google.com/neg`, restart the workload; and always read the INGRESS events, not just the cert status - the cert error can be downstream of an ingress translation failure.

## Related

- [[3 Resources/Cloud/GCP/Free built-in GCP domain Cloud Endpoints DNS maps name.endpoints.PROJECT.cloud.goog to an IP]]
