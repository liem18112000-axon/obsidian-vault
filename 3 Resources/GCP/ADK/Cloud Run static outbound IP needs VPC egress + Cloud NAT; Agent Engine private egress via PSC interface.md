---
title: "Cloud Run static outbound IP needs VPC egress + Cloud NAT; Agent Engine private egress via PSC interface"
created: 2026-09-03
type: howto
status: seedling
source: "research session 2026-09-03"
tags: [gcp, cloud-run, agent-engine, networking, vpc, cloud-nat, psc, static-ip]
---

# Cloud Run static outbound IP needs VPC egress + Cloud NAT; Agent Engine private egress via PSC interface

**Cloud Run networking:**
- *Ingress:* default public `*.run.app` on a Google-managed **anycast** front-end — no dedicated inbound IP per service. For a **static ingress IP**, front it with an external HTTPS Load Balancer holding a reserved global static IP. Ingress can be restricted to `internal` / `internal-and-cloud-load-balancing`.
- *Egress:* by default outbound uses a **rotating pool of Google-owned IPs that changes over time** — useless for third-party allowlists. For a **static outbound IP**: route egress through your VPC (**Direct VPC egress** or a **Serverless VPC Access connector**, which needs a dedicated `/28` subnet) → **Cloud NAT** with a **reserved static external IP**. Instance IPs come from a subnet in your VPC, so size the range for max instances.

**Agent Engine networking:**
- Runs in a **Google-managed tenant project** (you never see the VMs/IPs). Inbound is the regional **Vertex AI API endpoint** (IAM-gated) / Agent Gateway — not a per-instance IP.
- *Egress default:* public internet over a Google path.
- *Private egress:* **PSC interface (PSC-I)** creates a **network attachment in your VPC**; agent↔VPC traffic uses **RFC1918** addressing and stays on Google's backbone (never public internet). DNS peering supported.
- *Under a VPC-SC perimeter:* default internet egress is **blocked** (anti-exfiltration); you must build an explicit path — typically a **proxy VM (RFC1918) + Cloud NAT** inside the perimeter. Also caps max_instances at ≤100.

Relevant to `test-agent` on Cloud Run: if Atlassian/Bitbucket ever require IP allowlisting, this VPC-egress + Cloud NAT static-IP pattern is the fix. See [[ADK deploy targets compute matrix: Agent Engine vs Cloud Run vs GKE (CPU/GPU/TPU)]].

*Unverified:* exact PSC-I subnet CIDR sizing not confirmed from primary Google docs — check the setup guide before building.

## Related

- [[ADK deploy targets compute matrix: Agent Engine vs Cloud Run vs GKE (CPU/GPU/TPU)]]
