---
title: "Co-located --network host box hides cross-box firewall hops"
created: 2026-08-25
type: lesson
status: seedling
source: "session 2026-08-25 (leo-customer360 deployments)"
tags: [networking, security-group, docker, terraform, gotcha]
---

# Co-located --network host box hides cross-box firewall hops

When a platform co-locates every service on one box with Docker `--network host`, services reach each other on `127.0.0.1` and NO firewall rules are needed between them. Adding the **first dedicated box** silently breaks that assumption: those same hops now cross the network and must each be opened on the (shared) security group, which by default opens nothing inbound.

**Enumerate every hop the new box participates in, and note the direction — the rule is opened on the box being CONNECTED TO, sourced from the initiator's IP:**
- reverse-proxy -> new app port: open the app port (e.g. 8010) on the new box, source = the proxy box.
- new app -> its dependencies: open the dependency ports on the box that HOSTS them, source = the new box — e.g. app -> Redis (6580) opens on the Redis box; app -> trace/OTLP collector (4318) opens on the collector box.
- ops agents (e.g. Portainer agent 9001): open on the new box, source = the manager box.

**Gotcha:** on a shared security group, one `{port, cidr}` rule applies to ALL boxes in the group, so reason about it per-hop, keep each source a tight `/32`, and remember infra/firewall changes usually run out-of-band (CD pipelines rarely run infra Terraform). Also verify the new box's actual private IP after apply (DHCP) before hardcoding it in proxy upstreams / rules.

Surfaced moving CDP data-tracking-api from the shared api box to its own vServer.
