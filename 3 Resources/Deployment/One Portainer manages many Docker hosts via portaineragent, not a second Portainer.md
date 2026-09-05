---
title: "One Portainer manages many Docker hosts via portainer/agent, not a second Portainer"
created: 2026-08-23
type: lesson
status: seedling
source: "leo-customer360 deployments, session 2026-08-23"
tags: [portainer, docker, multi-node, security-group, leo-customer360]
---

# One Portainer manages many Docker hosts via portainer/agent, not a second Portainer

**Question:** platform spans multiple Docker hosts (vServers) — do you run one Portainer per box?

**Answer: no — one Portainer, many Agents.** A single Portainer manages any number of Docker hosts as *Environments*. On each additional box run the lightweight `portainer/agent` (~15-20 MB) and register it in the existing Portainer (UI: Environments -> Add -> Agent -> <host>:9001, or POST /api/endpoints with EndpointCreationType=2). Beats a second Portainer: one login, one UI, one RAM footprint. Match the agent image tag to the server (`portainer/agent:lts` with `portainer-ce:lts`).

**Connection model (standard Agent):** Portainer connects OUT to each agent at <host>:9001 over mTLS. So the agent's box must allow inbound :9001 from the Portainer box. If boxes can't be reached inbound (NAT, no port opening), use the **Edge Agent** instead (agent dials OUT to Portainer via a tunnel — no inbound port).

**Gotcha on locked-down clouds (e.g. VNG):** the default security group often 'opens nothing inbound', so intra-VPC :9001 is blocked until you add an explicit rule — source-restricted to the Portainer box's private IP (a /32), never 0.0.0.0/0. That rule is INFRA (terraform/console), typically applied out-of-band from the app/CD deploy that drops the agent container.

**Registration persists** in Portainer's data volume, so auto-registration only needs to succeed once and should be idempotent (check existing endpoints by name before POSTing).

Source: leo-customer360 deployments/monitoring (portainer_agent_server_keys) + deployments/server (extra_ingress tcp/9001), 2026-08.

## Related

- [[Exposure model for ops dashboards behind an L4 (OIDC-incapable) load balancer]]
