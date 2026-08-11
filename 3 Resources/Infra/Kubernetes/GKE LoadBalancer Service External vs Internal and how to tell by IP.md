---
ai_hash: ca1f9328ca70843e
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-03
entities: []
source: Vinnstack GKE deploy 2026-07-03
status: seedling
tags:
- kubernetes
- gke
- loadbalancer
- networking
title: 'GKE LoadBalancer Service: External vs Internal and how to tell by IP'
type: lesson
---

# GKE LoadBalancer Service: External vs Internal and how to tell by IP

A Kubernetes `type: LoadBalancer` Service on GKE is fronted by an **external (public)** L4 load balancer by default. To make it internal instead, annotate `networking.gke.io/load-balancer-type: "Internal"` (the modern annotation; legacy is `cloud.google.com/load-balancer-type`).

**Telling them apart at a glance by the assigned EXTERNAL-IP:** a public GCP forwarding-rule IP is in a routable range like `34.x.x.x` / `35.x.x.x`; an internal LB gets an RFC1918 VPC IP like `192.168.x.x` or `10.x.x.x`. So in a cluster where most Services show `192.168.1.x` external-IPs, those are deliberately-internal LBs (VPN/VPC-only), and a plain public Service will instead land a `34.x` address.

**Gotcha:** if a cluster/org defaults new LBs to internal (org policy or subnet convention), set `networking.gke.io/load-balancer-type: "External"` explicitly rather than relying on the GKE default, so you do not silently inherit an internal IP.

Provisioning is fast (~25s for the IP), but the LB health check can take another minute before traffic actually routes — poll the endpoint, do not assume a returned IP means reachable.

## Related

- [[GKE Immediate-binding StorageClass deadlocks a single-replica StatefulSet across zones]]

%% ai-graph-start %%

**Related notes:**
- [[GKE managed-cert HTTPS global IP, DNS before cert, NEG service, FrontendConfig redirect]]
- [[Free built-in GCP domain Cloud Endpoints DNS maps name.endpoints.PROJECT.cloud.goog to an IP]]
- [[Creating the GSA a KSA annotation references activates WI routing and can break a pod]]

%% ai-graph-end %%