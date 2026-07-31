---
title: "Arm a new login gate by env presence so shipping auth cannot lock the operator out"
created: 2026-07-04
type: lesson
status: seedling
source: "session 2026-07-04, vinnstack Google OAuth"
tags: [auth, oauth, rollout, vinnstack]
---

# Arm a new login gate by env presence so shipping auth cannot lock the operator out

When retrofitting login onto a live, previously-auth-less app, make the gate ENV-ACTIVATED: the whole-app sign-in requirement (client gate + middleware) arms only when the OAuth client env vars (GOOGLE_CLIENT_ID/SECRET) are present. Until then the app behaves exactly as before.

Why: the OAuth client is an external prerequisite the developer cannot create for the operator (GCP console, consent screen, redirect URIs). Shipping a hard gate before those exist bricks the app the operator is actively using; shipping the gate dark-but-armed-by-env lets the code merge now and the operator flips it on by setting env - no second deploy.

Corollaries: providers array is conditionally empty (NextAuth boots fine with zero providers); expose a tiny /auth/status endpoint returning {configured} so the client gate knows whether to arm; keep a machine-level credential fallback so logged-out/unconfigured behavior is the legacy path, and per-account storage takes precedence once sessions exist.

## Related

- [[A server-side action rejection is a stale-view signal - resync on error]]
