---
title: "Loopback-bind a bridge container that must reach a host-network service"
created: 2026-08-25
type: howto
status: seedling
source: "session 2026-08-25 (leo-customer360 redis viewer)"
tags: [docker, networking, host-gateway, security, gotcha]
---

# Loopback-bind a bridge container that must reach a host-network service

To expose an admin web UI **loopback-only** (for SSH-tunnel access) while it still needs to connect to another service that runs with Docker `--network host`, run the UI container on the **default bridge** and:

- publish it with `-p 127.0.0.1:PORT:CPORT` — this genuinely binds the UI to the host's loopback (nothing on the VPC/public can reach it; only `ssh -L PORT:localhost:PORT` does), and
- reach the host-network service via `--add-host host.docker.internal:host-gateway` (Docker >= 20.10), pointing the UI's connection string at `host.docker.internal:<service-port>`.

**Why not just `--network host` for the UI too?** Under host networking `-p` is ignored and most apps bind `0.0.0.0`, so you can't cleanly restrict the UI to loopback — it becomes reachable by every other host on the private subnet. The bridge + `127.0.0.1` publish is the clean way to get true loopback binding while still crossing into a host-net dependency.

Add HTTP basic auth as defense-in-depth regardless. Surfaced deploying redis-commander (loopback) against a `--network host` broker Redis on the CDP tracking box.

## Related

- [[Co-located --network host box hides cross-box firewall hops]]
