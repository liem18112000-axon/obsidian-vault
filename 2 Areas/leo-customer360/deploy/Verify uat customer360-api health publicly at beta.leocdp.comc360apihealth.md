---
title: "Verify uat customer360-api health publicly at beta.leocdp.com/c360api/health"
created: 2026-09-05
type: howto
status: seedling
source: "session 2026-09-05"
tags: [leo-customer360, uat, health-check, caddy, fastapi, ops]
---

# Verify uat customer360-api health publicly at beta.leocdp.com/c360api/health

The uat customer360-api can be health-checked **without an SSH tunnel** via the public Caddy route:

```
curl -sS https://beta.leocdp.com/c360api/health
# -> 200 {"status":"ok","database":"reachable","sso_login":true}
```

- The container itself listens on the VM at `:8008` (`--network host`) with health path `/health`, only reachable via `ssh -L 8008:localhost:8008 leocdp360@49.213.71.76`.
- Caddy fronts `beta.leocdp.com` (uat) and routes `/c360api/*` to the api **unstripped** (`handle /c360api/*`, not `handle_path`) because the FastAPI app sets `root_path=/c360api`. Starlette strips root_path for routing, so the app's `/health` route is publicly served at `/c360api/health`.
- Interpreting results: `database:reachable` confirms Postgres connectivity; `/c360api/` and `/c360api/docs` returning **401 Authentication required** is healthy (auth enforced), not an error.
- Other services (per Caddyfile): `/` -> frontend, `/auth` -> Keycloak, `/ads` -> ads-server, `/data` -> data-tracking (stripped), `/jaeger` -> trace UI. prod domain is `leocdp.com`.

Related: [[SHA-pinned docker pulls accumulate and fill small deploy VM disks]].

## Related

- [[SHA-pinned docker pulls accumulate and fill small deploy VM disks]]
