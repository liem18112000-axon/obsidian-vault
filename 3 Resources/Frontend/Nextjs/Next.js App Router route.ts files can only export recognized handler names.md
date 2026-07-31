---
ai_hash: c2127f946ec17615
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
aliases:
- Next.js route.ts files reject non-handler exports - shared helpers must live in
  lib
created: 2026-07-09
entities: []
source: sessions 2026-07-03 / 2026-07-09, vinnstack
status: seedling
tags:
- nextjs
- typescript
- app-router
- gotcha
title: Next.js App Router route.ts files can only export recognized handler names
type: lesson
---

# Next.js App Router route.ts files can only export recognized handler names

A `route.ts` file may export ONLY the recognized handler names (`GET`, `POST`, `PUT`, `DELETE`, `PATCH`, `HEAD`) plus route-config exports (`runtime`, `dynamic`, `revalidate`). Any other named export — a helper function, shared constant, type — fails the build's route typecheck, and not in your file: the error comes from the generated `.next/types/app/.../route.ts`, worded confusingly as `Property X is incompatible with index signature` / `does not satisfy the constraint {[x: string]: never}`.

It only surfaces on `next build` / `tsc --noEmit` **after** Next has generated its route type file — reading the route source gives no hint.

Fix: drop the `export` keyword if nothing outside needs it; otherwise move the helper to a `lib/` module and import it. This is the structural reason two routes that share a helper (e.g. a `safeUploadName` sanitizer and upload size limits) need a shared `lib/` module instead of route-to-route imports.

%% ai-graph-start %%

**Related notes:**
- [[NextAuth cannot share apiauth with an existing dynamic route - single segments get shadowed]]
- [[Next.js dev server webpack chunk cache corrupts after many route addsdeletes]]
- [[instrumentation.ts with a Node-only dep breaks the edge build - externalize it]]
- [[Hydration mismatches only surface as minified errors in production not dev]]

%% ai-graph-end %%