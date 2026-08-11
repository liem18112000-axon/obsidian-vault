---
ai_hash: 50d2309347c435bb
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-04
entities: []
source: session 2026-07-04, vinnstack Google OAuth
status: seedling
tags:
- nextjs
- nextauth
- routing
- vinnstack
title: NextAuth cannot share /api/auth with an existing dynamic route - single segments
  get shadowed
type: gotcha
---

# NextAuth cannot share /api/auth with an existing dynamic route - single segments get shadowed

In the Next.js App Router, a dynamic segment route (`/api/auth/[provider]`) beats a sibling catch-all (`/api/auth/[...nextauth]`) for single-segment paths. If the app already owns `/api/auth/[provider]`, dropping NextAuth's catch-all next to it silently breaks: `/api/auth/session`, `/api/auth/signin`, `/api/auth/csrf` all route to the provider handler (returning e.g. `404 unknown provider`), while two-segment paths like `/api/auth/callback/google` still reach NextAuth - a maddening half-working state.

Fix: give NextAuth its own base path (`app/api/nextauth/[...nextauth]/route.ts`) and align all three consumers:
1. `<SessionProvider basePath="/api/nextauth">` (sets the client module's base for useSession/signIn/signOut),
2. `NEXTAUTH_URL=https://host/api/nextauth` (server callback URL construction - path included),
3. the Google OAuth client's authorized redirect URI: `.../api/nextauth/callback/google`.
Miss any one and you get redirect_uri_mismatch or a session that never loads. Symptom to recognize: session endpoint 404s with a body from YOUR OWN route handler.

## Related

- [[Next.js App Router route.ts files can only export recognized handler names]]

%% ai-graph-start %%

**Related notes:**
- [[Next.js App Router route.ts files can only export recognized handler names]]
- [[Arm a new login gate by env presence so shipping auth cannot lock the operator out]]
- [[Next.js dev server webpack chunk cache corrupts after many route addsdeletes]]
- [[Per-account write silently skipped when the server cant resolve the session looks saved, isnt]]
- [[Local-app OAuth bridges the browser callback to the waiting login via an in-memory state-to-resolver map]]

%% ai-graph-end %%