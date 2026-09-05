---
title: "GKE API TCP timeout with working internet = IP dropped from master authorized networks"
created: 2026-09-04
type: lesson
status: seedling
source: "session 2026-09-04 (perf-cluster seed stall)"
tags: [gke, kubectl, networking, authorized-networks, gotcha, diagnostics]
---

# GKE API TCP timeout with working internet = IP dropped from master authorized networks

When `kubectl` fails with `Unable to connect to the server: dial tcp <API-IP>:443 ... failed to respond` (a **timeout**, not `connection refused` and not a 401/403), and general internet still works, the cause is almost always that your **public egress IP is no longer in the GKE clusters master authorized-networks allowlist** — the control plane silently drops packets from non-whitelisted sources, which presents as a TCP timeout rather than a clean refusal. Common triggers: your ISP/DHCP reassigned your IP overnight, or a VPN that provided the whitelisted IP disconnected/reconnected on a different exit.

**Diagnose in seconds** by discriminating cluster-specific vs whole-network loss: `Test-NetConnection <API-IP> -Port 443` returns False while `Test-NetConnection 8.8.8.8 -Port 443` and `oauth2.googleapis.com:443` return True → it is the cluster path, not your internet, and not gcloud auth (gcloud tokens refreshing fine rules auth out).

**Fix (user-side):** reconnect the VPN, or re-add your current public IP to the clusters authorized networks (`gcloud container clusters update <cluster> --master-authorized-networks <cidr>` with admin rights). Auth (`gcloud auth`) is a red herring here — the failure is network-path, not credentials. Bit a long overnight GKE data seed; see [[Decouple long agent work from the harness task lifecycle]] and [[Resume a large append seed by recounting to a target]] for making such a seed resume loss-free once the path is restored.

## Related

- [[Decouple long agent work from the harness task lifecycle]]
- [[Resume a large append seed by recounting to a target]]
