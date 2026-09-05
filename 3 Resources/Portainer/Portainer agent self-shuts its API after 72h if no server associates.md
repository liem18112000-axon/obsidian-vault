---
title: "Portainer agent self-shuts its API after 72h if no server associates"
created: 2026-08-31
type: lesson
status: seedling
source: "session 2026-08-31 tracking env missing in Portainer"
tags: [portainer, docker, monitoring, gotcha, leo-customer360]
---

# Portainer agent self-shuts its API after 72h if no server associates

A `portainer/agent` (seen on 2.39.6) that no Portainer **server** ever *associates* with (i.e. the environment was never registered/connected in the server) **shuts down its own API server after ~72h**, logging:

```
shutting down API server as no client was associated after the timeout, keeping alive to prevent restart by docker/kubernetes | timeout=259200000
```

`259200000 ms = 72h`. It deliberately keeps the **container alive** (so Docker/K8s do not restart-loop it) but stops serving. The trap: a plain TCP probe to `:9001` still **succeeds** because Docker's `docker-proxy` accepts the connection — but the agent API behind it is dead, so the environment shows nothing / appears down. Diagnose with `docker logs c360-portainer-agent` (look for the "no client was associated" line), not a port check.

## Fix
1. `docker restart <agent>` — revives the API server and resets the 72h timer.
2. Ensure the Portainer server box can reach the agent over the VPC (`curl -sk https://<priv-ip>:9001/ping` should return **204**).
3. **Register the environment** in the server (the real fix — this is what was missing): `POST /api/endpoints` with `EndpointCreationType=2`, `URL=tcp://<priv-ip>:9001`, `TLS=true`, `TLSSkipVerify=true`. Once registered, the server polls it regularly, keeping it "associated" so it never self-shuts again.

## leo-customer360 specifics
`deployments/monitoring/deploy-monitoring.sh` runs the agent for each key in `portainer_agent_server_keys` (uat `"1x2,tracking"`) and registers it as env `c360-<key>` — but the registration is **guarded on `PORTAINER_ADMIN_PASSWORD` being exported** (from `monitoring/.env`). If that var is unset at deploy time, the agent is deployed but never registered → 72h self-shutdown. Endpoint name convention: `c360-tracking` → `tcp://10.100.1.8:9001`. Related: [[Scale one uvicorn service into N replicas on one VM with a docker bridge + local nginx LB]].

## Related

- [[Scale one uvicorn service into N replicas on one VM with a docker bridge + local nginx LB]]
