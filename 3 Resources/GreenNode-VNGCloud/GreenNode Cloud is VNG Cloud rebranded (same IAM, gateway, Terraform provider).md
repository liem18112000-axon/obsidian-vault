---
title: "GreenNode Cloud is VNG Cloud rebranded (same IAM, gateway, Terraform provider)"
created: 2026-08-17
type: concept
status: seedling
source: "session 2026-08-17"
tags: [greennode, vngcloud, cloud, iam]
---

# GreenNode Cloud is VNG Cloud rebranded (same IAM, gateway, Terraform provider)

GreenNode (greennode.ai) is VNG Corporation's international/AI-cloud brand built on the **same platform as VNG Cloud** (vngcloud.vn). They are not separate stacks — a GreenNode tenant authenticates against VNG Cloud's IAM and hits VNG Cloud's service gateways.

Concretely, GreenNode's own vStorage API docs authenticate at `https://iamapis.vngcloud.vn/accounts-api/v2/auth/token` (OAuth2 `client_credentials`, HTTP Basic `client_id:client_secret`, returns a 30-min Bearer token). The managed-DB (vDB) gateway is `https://vdb-gateway.vngcloud.vn`; compute/LB gateways are under `hcm-3.api.vngcloud.vn`.

**Why it matters:** tooling and docs written for VNG Cloud (the Terraform provider, API endpoints, IAM concepts) apply to GreenNode accounts. When automating GreenNode, point at the VNG Cloud endpoints unless the tenant was explicitly issued different gateway hostnames (confirm via browser DevTools on the console). Regions seen: HCM03-1A/1B/1C.

See [[Provision GreenNode/VNG Cloud vDB PostgreSQL with the vngcloud Terraform provider]].

## Related

- [[Provision GreenNode/VNG Cloud vDB PostgreSQL with the vngcloud Terraform provider]]
