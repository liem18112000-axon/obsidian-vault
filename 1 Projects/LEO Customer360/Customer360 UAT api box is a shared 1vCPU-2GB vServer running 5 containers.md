---
title: "Customer360 UAT api box is a shared 1vCPU/2GB vServer running 5 containers"
created: 2026-08-19
type: reference
status: seedling
source: "session 2026-08-19"
tags: [customer360, deployment, vngcloud, greennode, docker, topology, uat]
---

# Customer360 UAT api box is a shared 1vCPU/2GB vServer running 5 containers

In the `leo-customer360` repo, the UAT deployment co-locates five services as Docker
containers on ONE small VM — the `api` server key, flavor **`s-general-1x2` = 1 vCPU /
2 GB RAM / 20 GB disk** (HCM03-1C), defined in `deployments/server/overlays/uat.tfvars`.

All run with `--network host --restart unless-stopped`:

| Service | Container name | Host port(s) | Health |
|---|---|---|---|
| redis | `c360-redis` | 6580 | `redis-cli PING` |
| api-server | `customer360-api` | 8008 | `/health` |
| ads-server | `customer360-ads` | 9009 | `/health` |
| keycloak (SSO) | `c360-keycloak` | 8080 + **9000** (mgmt/health) | `:9000/health/ready` |
| frontend-admin | `customer360-frontend` | 8890 | `/` |

Keycloak is the JVM heavyweight (~400–700 MB). In **prod** these split onto dedicated
vServers (server keys `ads`, `sso`, `frontend`); UAT shares the one box to save cost.

**Gotcha — host networking means ports collide.** Anything new added to this box must
dodge 6580 / 8008 / 8080 / **9000** / 8890 / 9009. Notably Keycloak owns **:9000**, so
don't put Portainer (legacy HTTP default 9000) or cAdvisor (default 8080) there.

Deploy scripts: `deployments/{cache,server,ads-server,sso,frontend}/deploy-*.sh`; each
discovers the target VM's floating IP from `../server` Terraform outputs by server key.

Related: [[Monitoring the Customer360 box - self-hosted Grafana is free, cost is resource pressure]] · [[LEO Customer360 GreenNode Terraform infrastructure]]
