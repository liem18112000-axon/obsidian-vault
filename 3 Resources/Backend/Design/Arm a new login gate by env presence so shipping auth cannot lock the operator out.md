---
ai_hash: 6e0a6c226926e9dd
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-04
entities: []
source: session 2026-07-04, vinnstack Google OAuth
status: seedling
tags:
- auth
- oauth
- rollout
- vinnstack
title: Arm a new login gate by env presence so shipping auth cannot lock the operator
  out
type: lesson
---

# Arm a new login gate by env presence so shipping auth cannot lock the operator out

When retrofitting login onto a live, previously-auth-less app, make the gate ENV-ACTIVATED: the whole-app sign-in requirement (client gate + middleware) arms only when the OAuth client env vars (GOOGLE_CLIENT_ID/SECRET) are present. Until then the app behaves exactly as before.

Why: the OAuth client is an external prerequisite the developer cannot create for the operator (GCP console, consent screen, redirect URIs). Shipping a hard gate before those exist bricks the app the operator is actively using; shipping the gate dark-but-armed-by-env lets the code merge now and the operator flips it on by setting env - no second deploy.

Corollaries: providers array is conditionally empty (NextAuth boots fine with zero providers); expose a tiny /auth/status endpoint returning {configured} so the client gate knows whether to arm; keep a machine-level credential fallback so logged-out/unconfigured behavior is the legacy path, and per-account storage takes precedence once sessions exist.

## Related

- [[A server-side action rejection is a stale-view signal - resync on error]]

%% ai-graph-start %%

**Related notes:**
- [[Separate read-check from create in a first-run onboarding gate]]
- [[Gate a headless-only relay behind an explicit env flag so local browser login still works]]
- [[NextAuth cannot share apiauth with an existing dynamic route - single segments get shadowed]]
- [[Vinnstack desktop app dropped Google OAuth for a typed-email operator identity]]
- [[Per-account write silently skipped when the server cant resolve the session looks saved, isnt]]

%% ai-graph-end %%