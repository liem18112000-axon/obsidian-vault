---
title: "Caddy path matcher /p/* does not match the bare /p; use a named matcher for both"
created: 2026-08-20
type: gotcha
status: seedling
source: "leo-customer360 proxy Caddyfile, 2026-08"
tags: [caddy, path-matcher, routing, trailing-slash, gotcha]
---

# Caddy path matcher /p/* does not match the bare /p; use a named matcher for both

In Caddy, the path matcher `/p/*` matches `/p/` and anything below it, but NOT the bare `/p` (no trailing slash). So `handle /p/* { reverse_proxy ... }` silently misses a request to exactly `/p`, which then falls through to a later catch-all (e.g. a SPA) and 404s — surprising when `/p/anything` works fine.

**Fix:** match both with a named matcher: `@p path /p /p/*` then `handle @p { ... }`. (Caddys `path` matcher takes multiple patterns; list the exact path and the wildcard.) Alternatively `redir /p /p/ 308` to bounce the bare form. Symptom that fingerprints it: `/p/...` reaches the upstream but exactly `/p` returns the catch-all/frontends 404.

Concrete: `https://host/auth` (Keycloak under KC_HTTP_RELATIVE_PATH=/auth) 404d from the frontend while `/auth/` and `/auth/realms/...` worked; fixed with `@auth path /auth /auth/*`.

Related: [[Caddy handle_path strips the path prefix, handle keeps it]]

## Related

- [[Caddy handle_path strips the path prefix]]
- [[handle keeps it]]
