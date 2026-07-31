---
title: "Local-app OAuth bridges the browser callback to the waiting login via an in-memory state-to-resolver map"
created: 2026-07-01
type: howto
status: seedling
source: "session 2026-07-01 (Vinnstack Bitbucket OAuth)"
tags: [oauth, bitbucket, nextjs, local-app, pattern, vinnstack]
---

# Local-app OAuth bridges the browser callback to the waiting login via an in-memory state-to-resolver map

OAuth 2.0 (3LO authorization-code) browser sign-in in a LOCAL single-process app (e.g. Next dev on localhost) spans TWO separate HTTP requests: the app's `login()` call, and the vendor's redirect to your callback route. Bridge them with an in-memory map keyed by the OAuth `state`.

- `login()`: generate `state` (`crypto.randomBytes`), `pending.set(state, resolve)`, open the browser (`cmd /c start` on Windows, `open`/`xdg-open` else), return a Promise that resolves when the callback fires (with timeout + abort).
- `GET /callback?code&state`: `pending.get(state)`, exchange code -> tokens (POST token endpoint, Basic `client_id:secret`, form body `grant_type=authorization_code`), persist tokens, call `resolver(ok)` to unblock `login()`, return a tiny self-closing HTML page.
- Refresh: same endpoint with `grant_type=refresh_token`; store `expires_at` and refresh ~60s early.

Works only because both requests hit the same process (the map is shared memory). Fine for a local desktop app; use a shared store on a scaled/multi-worker server.

Bitbucket specifics: authorize `https://bitbucket.org/site/oauth2/authorize?client_id&response_type=code&state&redirect_uri`; token `https://bitbucket.org/site/oauth2/access_token`. Requires a workspace OAuth **consumer** (client_id/secret) with the callback URL registered - a one-time admin step with no way around it.

Gotcha: a NEW deeply-nested API route folder added while `next dev` is running may not hot-register (Windows watcher) and 404s until the dev server restarts, even though shallower new routes are picked up.

Real case: Vinnstack Bitbucket provider (`lib/authProviders.ts` + `app/api/auth/callback/bitbucket`).
