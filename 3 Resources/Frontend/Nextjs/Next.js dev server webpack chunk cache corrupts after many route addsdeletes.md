---
title: "Next.js dev server webpack chunk cache corrupts after many route adds/deletes"
created: 2026-07-07
type: lesson
status: seedling
source: "Vinnstack session 2026-07-07"
tags: [nextjs, webpack, dev-server, gotcha]
---

# Next.js dev server webpack chunk cache corrupts after many route adds/deletes

If a `next dev` server stays running while many API routes are added/deleted/renamed in one editing session, its incremental webpack dev-build cache can end up referencing chunk files that no longer exist. The symptom is a server-side crash on the NEXT request to any route (not necessarily the one that changed): `Error: Cannot find module './<some-number>.js'` with the require stack pointing through `.next/server/webpack-runtime.js`. The error is `MODULE_NOT_FOUND` but it is NOT a real missing dependency — it is a stale/corrupted incremental build artifact.

Fix: stop the dev server process entirely (a plain hot-reload will not self-heal this), delete the `.next` directory, and restart `next dev` so it recompiles every route from scratch with consistent chunk IDs.

This came up in the Vinnstack project after one editing session deleted ~6 API routes (`/api/vertex/models`, `/api/nextauth/[...nextauth]`, `/api/auth/google-status`, `/api/account/credentials`, `/api/electron-auth/*`) and added a couple of new ones (`/api/account/activate`) while `next dev` (via `npm run electron:dev`, which wraps it with `concurrently`) was left running throughout. The crash surfaced on an unrelated route (`/api/account/activate`) when the user clicked a button, not on the routes that were actually touched — which is the tell that it is a global webpack-runtime cache issue rather than a bug in that specific route.

General rule of thumb: after a large batch of route/file structural changes (adds, deletes, renames — not just edits), restart the dev server rather than trusting hot-reload to keep up.
