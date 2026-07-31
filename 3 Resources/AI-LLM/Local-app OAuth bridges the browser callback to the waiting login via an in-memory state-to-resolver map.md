---
title: "Local-app OAuth bridges the browser callback to the waiting login via an in-memory state-to-resolver map"
created: 2026-07-01
type: howto
status: seedling
source: "session 2026-07-01"
tags: [oauth, bitbucket, nextjs, local-app, pattern, vinnstack]
---

# Local-app OAuth bridges the browser callback to the waiting login via an in-memory state-to-resolver map

OAuth 2.0 (3LO authorization-code) browser sign-in in a LOCAL single-process app (e.g. Next dev on localhost) spans TWO HTTP requests: the app login() call and the vendor redirect to your callback route. Bridge them with an in-memory map keyed by the OAuth state: login() generates state (crypto.randomBytes), stores pending.set(state, resolve), opens the browser (cmd /c start on Windows; open/xdg-open else), and returns a Promise resolved when the callback fires (with timeout + abort). GET /callback?code&state looks up the resolver, exchanges code->tokens (POST token endpoint, Basic client_id:secret, form grant_type=authorization_code), persists tokens, calls resolver(ok), returns a self-closing HTML page. Refresh: grant_type=refresh_token; store expires_at, refresh ~60s early. Shared-memory map works only because both requests hit the same process (fine for a local desktop app; use a shared store for scaled servers). Bitbucket: authorize https://bitbucket.org/site/oauth2/authorize, token https://bitbucket.org/site/oauth2/access_token; requires a workspace OAuth consumer (client_id/secret) with the callback URL registered (one-time, unavoidable). Gotcha: a NEW deeply-nested API route folder added while next dev runs may not hot-register on Windows (404 until restart). Real case: Vinnstack Bitbucket provider.
