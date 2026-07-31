---
title: "NextAuth cannot share /api/auth with an existing dynamic route - single segments get shadowed"
created: 2026-07-04
type: gotcha
status: seedling
source: "session 2026-07-04, vinnstack Google OAuth"
tags: [nextjs, nextauth, routing, vinnstack]
---

# NextAuth cannot share /api/auth with an existing dynamic route - single segments get shadowed

In the Next.js App Router, a dynamic segment route (`/api/auth/[provider]`) beats a sibling catch-all (`/api/auth/[...nextauth]`) for single-segment paths. If the app already owns `/api/auth/[provider]`, dropping NextAuth's catch-all next to it silently breaks: `/api/auth/session`, `/api/auth/signin`, `/api/auth/csrf` all route to the provider handler (returning e.g. `404 unknown provider`), while two-segment paths like `/api/auth/callback/google` still reach NextAuth - a maddening half-working state.

Fix: give NextAuth its own base path (`app/api/nextauth/[...nextauth]/route.ts`) and align all three consumers:
1. `<SessionProvider basePath="/api/nextauth">` (sets the client module's base for useSession/signIn/signOut),
2. `NEXTAUTH_URL=https://host/api/nextauth` (server callback URL construction - path included),
3. the Google OAuth client's authorized redirect URI: `.../api/nextauth/callback/google`.
Miss any one and you get redirect_uri_mismatch or a session that never loads. Symptom to recognize: session endpoint 404s with a body from YOUR OWN route handler.

## Related

- [[Next.js route.ts files reject non-handler exports - shared helpers must live in lib]]
