---
title: "Monitoring the Customer360 box - self-hosted Grafana is free, cost is resource pressure"
created: 2026-08-19
type: note
status: seedling
source: "session 2026-08-19"
tags: [monitoring, observability, grafana, prometheus, netdata, portainer, cost, customer360]
---

# Monitoring the Customer360 box: self-hosted Grafana is free, the cost is resource pressure

When asked "does a full Grafana/Prometheus stack cost more money?" the honest split is
**licence cost vs resource cost**:

- **Licence: $0.** Prometheus, Grafana OSS, cAdvisor, node_exporter, redis_exporter,
  blackbox_exporter are all free (Apache-2.0 / AGPL), self-hosted on a VM you already
  pay for. Only **Grafana Cloud / Grafana Enterprise / managed Prometheus** cost money —
  and you wouldn't be using those.
- **Resource cost is the real one.** The full stack is ~6 containers adding **~0.5–1 GB
  RAM + steady CPU** (cAdvisor continuously walks cgroups — painful on 1 vCPU) + a
  growing Prometheus TSDB on disk.

**Why it matters here:** the Customer360 UAT box is only `s-general-1x2` (1 vCPU / 2 GB /
20 GB) and already runs Keycloak (JVM) + Redis + 3 FastAPI apps — see
[[Customer360 UAT api box is a shared 1vCPU-2GB vServer running 5 containers]]. Adding the
full stack would trigger swap/OOM and force a **VM flavor upsize** — and that upsize is
the only place money actually appears.

**Rule of thumb:** on a tiny/loaded box, prefer a single lightweight agent —
**Netdata** (~100–200 MB, no separate TSDB, real-time dashboard + alarms) or **Portainer**
(~50 MB, container ops UI). Reserve Prometheus+Grafana for when monitoring gets its own
small VM or the box is grown for other reasons. Self-hosting = free software, never free
RAM/CPU.
