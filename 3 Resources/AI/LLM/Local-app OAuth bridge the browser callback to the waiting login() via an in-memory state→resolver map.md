---
title: "Local-app OAuth: bridge the browser callback to the waiting login() via an in-memory state→resolver map"
created: 2026-07-01
type: howto
status: seedling
source: "session 2026-07-01 (Vinnstack Bitbucket OAuth)"
tags: [oauth, bitbucket, nextjs, local-app, pattern, vinnstack]
---

# Local-app OAuth: bridge the browser callback to the waiting login() via an in-memory state→resolver map

Implementing OAuth 2.0 (3LO authorization-code) browser sign-in in a LOCAL app (single Node process, e.g. Next dev on localhost) — the flow spans TWO separate HTTP requests: the app's login() call, and the vendor's redirect to your callback route. Bridge them with an in-memory map keyed by the OAuth `state`:

- login(): generate `state` (crypto.randomBytes), store `pending.set(state, resolve)`, open the browser (`cmd /c start` on Windows, `open`/`xdg-open` else), and return a Promise that resolves when the callback fires (with a timeout + abort). 
- GET /callback?code&state: look up `pending.get(state)`, exchange code→tokens (POST token endpoint, Basic client_id:secret, form body grant_type=authorization_code), persist tokens, then `resolver(ok)` to unblock login(). Return a tiny self-closing HTML page.
- Refresh: same token endpoint with grant_type=refresh_token; store expires_at and refresh ~60s early.

Works because both requests hit the same process (the map is shared memory). Would break across multiple workers/instances — fine for a local desktop app, not for a scaled server (use a shared store there).

Bitbucket specifics: authorize `https://bitbucket.org/site/oauth2/authorize?client_id&response_type=code&state&redirect_uri`; token `https://bitbucket.org/site/oauth2/access_token`. Requires a workspace OAuth **consumer** (client_id/secret) with the callback URL registered — a one-time admin setup; there's no way around registering a consumer.

Gotcha: adding a NEW deeply-nested API route folder while `next dev` is running may not be hot-registered (Windows watcher) — it 404s until you restart the dev server, even though shallower new routes get picked up.

Real case: Vinnstack Bitbucket provider (lib/authProviders.ts + app/api/auth/callback/bitbucket).
