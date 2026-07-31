---
ai_hash: 78740202c4f28e61
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-01
entities: []
source: session 2026-07-01 (Vinnstack Bitbucket OAuth)
status: seedling
tags:
- oauth
- bitbucket
- nextjs
- local-app
- pattern
- vinnstack
title: Local-app OAuth bridges the browser callback to the waiting login via an in-memory
  state-to-resolver map
type: howto
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

%% ai-graph-start %%

**Related notes:**
- [[Atlassian Cloud OAuth 3LO specifics JSON token body, rotating refresh, cloudId via accessible-resources]]
- [[Local-app provider sign-in drive the vendor CLI; Vertex is the exception (gcloud ADC + projectregion)]]
- [[Vinnstack provider abstraction enables pluggable auth without UIroute changes]]
- [[Arm a new login gate by env presence so shipping auth cannot lock the operator out]]
- [[NextAuth cannot share apiauth with an existing dynamic route - single segments get shadowed]]

%% ai-graph-end %%