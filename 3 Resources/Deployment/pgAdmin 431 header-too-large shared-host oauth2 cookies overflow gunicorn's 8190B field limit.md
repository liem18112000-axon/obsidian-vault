---
title: "pgAdmin 431 header-too-large: shared-host oauth2 cookies overflow gunicorn's 8190B field limit"
created: 2026-08-23
type: lesson
status: seedling
source: "leo-customer360 deployments/monitoring, session 2026-08-23"
tags: [pgadmin, gunicorn, oauth2-proxy, cookies, http-431, gotcha, leo-customer360]
---

# pgAdmin 431 header-too-large: shared-host oauth2 cookies overflow gunicorn's 8190B field limit

**Symptom:** pgAdmin (dpage/pgadmin4) returns HTTP **431 "Request Header Fields Too Large"**; container log shows gunicorn `WARNING ... limit request headers fields size`. It happens intermittently / for some users, often after they've used other tools on the same host.

**Root cause = host-scoped cookies + a strict per-field limit.** pgAdmin runs under **gunicorn**, whose default `limit_request_field_size` is **8190 bytes per header field**. The `Cookie:` header is a SINGLE field. When several web tools are co-located on one host/IP (here Portainer, Netdata, Jaeger, pgAdmin on 103.245.254.29), the large `_oauth2_*` session cookies that **oauth2-proxy** sets for the gated tools get sent to ALL of them — because **cookies are scoped by host, NOT by port**. The concatenated Cookie header blows past 8190B and gunicorn rejects the request before pgAdmin ever sees it.

**Why pgAdmin is the one that breaks:** gunicorn's 8190B per-field default is stricter than most reverse proxies / app servers, so the shared-cookie bloat trips pgAdmin first while the other tools keep working.

**Fix (server-side, durable):** raise gunicorn's limit on the pgAdmin container via the env var gunicorn reads natively:
```
-e GUNICORN_CMD_ARGS="--limit-request-field_size 65535 --limit-request-fields 200"
```
(Note the gunicorn flag spelling: `--limit-request-field_size` has an underscore.) Verify: send a ~10-30KB `Cookie:` header with curl — should stop returning 431.

**Immediate user-side relief:** clear cookies for the host (or use a private window) — but that recurs the next time they log into a sibling oauth2-gated tool on the same host, so the server-side raise is the real fix.

**Deeper lesson:** co-locating multiple web apps on one hostname/IP couples their cookie namespaces. Big auth cookies from one leak to all. Long-term fixes: give each tool its own hostname, or set narrower cookie Path/Domain. Short-term: make the strict-limit app tolerate bigger headers.

Source: leo-customer360 deployments/monitoring, pgAdmin direct-on-LB (2026-08).

## Related

- [[Exposure model for ops dashboards behind an L4 (OIDC-incapable) load balancer]]
