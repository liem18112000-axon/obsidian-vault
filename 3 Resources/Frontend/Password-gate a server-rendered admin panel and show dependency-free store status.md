---
ai_hash: ed2a2cf96cfc21ad
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-14
entities: []
source: session 2026-06-14, accesstrade_integration admin panel
status: seedling
tags:
- fastapi
- htmx
- admin
- security
- hmac
- redis
- postgres
title: Password-gate a server-rendered admin panel and show dependency-free store
  status
type: howto
---

# Password-gate a server-rendered admin panel and show dependency-free store status

**For a lightweight admin panel in a server-rendered (no-SPA / HTMX) app: gate it with a password from an env secret checked CONSTANT-TIME (`hmac.compare_digest`), render the protected fragment only on a match (no session needed — the password is posted per unlock), and show data-store status with a DEPENDENCY-FREE TCP reachability probe rather than pulling in DB/cache client libraries.**

Pieces (Accesstrade Console System page):
- Gate: `hmac.compare_digest(supplied, configured)`; return False when `configured` is empty so an unset secret can NEVER unlock (admin disabled, not open). Constant-time avoids timing oracles on the password.
- No session: the HTMX form POSTs the password; on match the server returns the admin HTML fragment; on miss a 403 fragment. Nothing stored. Simple and good enough for an internal console (serve over HTTPS since the password is posted).
- Status without drivers: parse `DATABASE_URL`/`REDIS_URL` with `urllib.parse.urlsplit`, then `socket.create_connection((host, port), timeout)` → reachable/unreachable. This reports *reachability* (host:port open) without psycopg/redis-py. A true auth-level check needs the driver; call out that limitation.
- Redaction: NEVER render the store password. Show host/port/db/user and just '•••••• (set)'. urlsplit exposes `.password` — make sure it never lands in the template or any `str(info)`.
- Default ports: fill missing port from the scheme (postgres→5432, redis→6379) so the probe still works when the URL omits it.

Testing tip: point the probe at `127.0.0.1:1` (closed) so the reachability check fails FAST (ECONNREFUSED) instead of hanging on a DNS/timeout in unit tests. Relates to [[Secrets handling for affiliate API keys]].

%% ai-graph-start %%

**Related notes:**
- [[Arm a new login gate by env presence so shipping auth cannot lock the operator out]]
- [[Docker hostname for reaching a service depends on where the caller runs]]
- [[urllib SplitResult.port raises ValueError on a non-numeric port]]

%% ai-graph-end %%