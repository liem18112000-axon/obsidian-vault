---
title: "A Google-IAP-protected site cannot be embedded in a cross-origin iframe (403)"
created: 2026-07-14
type: lesson
status: seedling
source: "session 2026-07-14"
tags: [iframe, iap, sso, auth, gotcha, csp]
---

# A Google-IAP-protected site cannot be embedded in a cross-origin iframe (403)

Gotcha (Vinnstack Polaris workspace, 2026-07-14): tried to embed polaris.epost.ch in an <iframe>; it rendered Google's "403. That's an error. We're sorry, but you do not have access to this page." page.

Why: the site is behind Google sign-in / IAP (Identity-Aware Proxy). IAP authenticates via a cookie. In a cross-origin iframe (app served from localhost, frame from *.epost.ch), the browser treats that cookie as third-party and does NOT send it (third-party-cookie blocking + SameSite), so IAP sees an unauthenticated request and returns 403. Note this is DIFFERENT from an X-Frame-Options / CSP frame-ancestors block: framing was allowed (the 403 page rendered inside the frame) — it's the AUTH that fails, not the embedding.

Takeaway: you generally CANNOT embed an IAP/SSO-protected third-party app in a cross-origin iframe. Options: (1) open it in a real browser tab/window where the top-level Google session applies (a launcher, not an embed) — what we did; (2) reverse-proxy it under your own origin and forward auth (heavy, and usually defeats IAP's point); (3) host under the same parent domain so the cookie is first-party. For an internal SSO app, the launcher is the pragmatic answer.
