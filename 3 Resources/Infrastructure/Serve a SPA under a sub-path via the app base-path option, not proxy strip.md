---
title: "Serve a SPA under a sub-path via the app base-path option, not proxy strip"
created: 2026-08-25
type: lesson
status: seedling
source: "session 2026-08-25 (leo-customer360 /redis)"
tags: [reverse-proxy, caddy, spa, redis-commander, auth, gotcha]
---

# Serve a SPA under a sub-path via the app base-path option, not proxy strip

To serve a single-page app (SPA) under a sub-path behind a reverse proxy, do NOT strip the prefix — set the app'\''s own base-path option and forward the prefix UNSTRIPPED.

A SPA'\''s HTML references assets by relative or root-absolute paths and its JS calls its API at a fixed base. If the proxy strips `/foo`, the browser then requests `/bootstrap/...` or `/apiv2/...` at the ROOT, which the proxy routes to the wrong upstream (usually the catch-all) → broken page. Instead:
- set the app'\''s base-path env (redis-commander `URL_PREFIX=/redis`, Jaeger `QUERY_BASE_PATH=/jaeger`, Dagster `--path-prefix`, Grafana `root_url`), and
- use an UNSTRIPPED proxy block (Caddy `handle` / `@matcher path /foo /foo/*`, not `handle_path`), matching BOTH the bare `/foo` and `/foo/*` (bare usually 301s to `/foo/`).

**redis-commander auth gotcha found doing this:** setting `HTTP_USER`/`HTTP_PASSWORD` does NOT produce HTTP Basic Auth (no `WWW-Authenticate` header, and static `/` stays 200). redis-commander uses a **form-based Sign-In page + session**; its data API (`/apiv2/*`) returns 401 until you sign in through the web form. So `curl -u user:pass` on `/apiv2` stays 401 even with correct creds — that'\''s expected, not a misconfig. The security check that matters: unauthenticated `/apiv2/*` = 401 (data not public). Diagnostic tell: **a 401 with no `WWW-Authenticate` header is app/session auth, not HTTP Basic.**

## Related

- [[L4 LB: expose own-login UIs directly]]
- [[gate no-auth UIs behind oauth2-proxy]]
