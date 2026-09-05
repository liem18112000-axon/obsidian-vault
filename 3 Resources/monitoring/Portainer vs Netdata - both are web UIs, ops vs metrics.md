---
title: "Portainer vs Netdata - both are web UIs, ops vs metrics"
created: 2026-08-19
type: reference
status: seedling
source: "session 2026-08-19"
tags: [monitoring, portainer, netdata, docker, ui, observability]
---

# Portainer vs Netdata: both are web UIs, ops vs metrics

Both are single-container tools that ship their **own browser dashboard** — no build, no
config. They complement each other:

- **Portainer** = container **operations** UI. `https://<host>:9443`. First visit forces
  you to create an admin user/password. Shows container status/health, **live logs**,
  **exec/console into a container**, per-container CPU/mem graphs, images/volumes/networks,
  and start/stop/restart buttons. The "show me logs / restart it" tool.
- **Netdata** = real-time **metrics** UI. `http://<host>:19999`. **No login** by default.
  Auto-generated per-second charts: host CPU/RAM/disk/net, per-container cgroup metrics
  (auto-discovers containers), Redis/Postgres collectors, alarms. The "watch the graphs"
  tool.

**Gotchas:**
- **Netdata's :19999 dashboard is unauthenticated by default** — anyone who can reach the
  port sees everything. Keep it behind an SSH tunnel or add basic-auth before exposing it.
- **Portainer setup times out**: if you don't set the admin password within a few minutes
  of the container starting, it locks the init screen for security and you must
  `docker restart portainer` before you can set it.
- On a box where the app ports aren't public, reach both via SSH local-forward
  (`ssh -L 9443:localhost:9443 -L 19999:localhost:19999 user@host`).

Related: [[Monitoring the Customer360 box - self-hosted Grafana is free, cost is resource pressure]]
