---
title: "Per-account write silently skipped when the server cant resolve the session looks saved, isnt"
created: 2026-07-04
type: gotcha
status: seedling
source: "session 2026-07-04, vinnstack account_credentials empty"
tags: [auth, session, silent-skip, vinnstack]
---

# Per-account write silently skipped when the server cant resolve the session looks saved, isnt

A save path that mirrors to a per-account row ONLY when a server-side session resolves (if (authConfigured() && getServerSession()->id) save()) has a nasty failure mode: when the session ISN'T resolved server-side, the machine-level save still succeeds, the card shows "connected", and the account row is silently NOT written — the user sees success but the DB stays empty. The `if (id)` guard turns a should-be error into a no-op.

Diagnosis, decisive and zero-code: hit the account endpoint (GET /api/account/credentials) IN THE LOGGED-IN BROWSER. `{ok:true, credentials}` = session resolves (look elsewhere: stale dev server, wrong button); `{ok:false, error:"not signed in"}` = the route cannot see the session (the real bug — check NEXTAUTH_URL, NEXTAUTH_SECRET consistency, cookie name under a custom basePath, getServerSession(authOptions) parity with the [...nextauth] handler).

Fix the UX too: when configured-but-no-session, RETURN an explicit error instead of a silent skip, so "saved to machine but not your account" is visible rather than masquerading as success.

## Related

- [[A server-side action rejection is a stale-view signal - resync on error]]
