---
title: "Next.js App Router route.ts files can only export recognized handler names"
aliases: ["Next.js route.ts files reject non-handler exports - shared helpers must live in lib"]
created: 2026-07-09
type: lesson
status: seedling
source: "sessions 2026-07-03 / 2026-07-09, vinnstack"
tags: [nextjs, typescript, app-router, gotcha]
---

# Next.js App Router route.ts files can only export recognized handler names

A `route.ts` file may export ONLY the recognized handler names (`GET`, `POST`, `PUT`, `DELETE`, `PATCH`, `HEAD`) plus route-config exports (`runtime`, `dynamic`, `revalidate`). Any other named export — a helper function, shared constant, type — fails the build's route typecheck, and not in your file: the error comes from the generated `.next/types/app/.../route.ts`, worded confusingly as `Property X is incompatible with index signature` / `does not satisfy the constraint {[x: string]: never}`.

It only surfaces on `next build` / `tsc --noEmit` **after** Next has generated its route type file — reading the route source gives no hint.

Fix: drop the `export` keyword if nothing outside needs it; otherwise move the helper to a `lib/` module and import it. This is the structural reason two routes that share a helper (e.g. a `safeUploadName` sanitizer and upload size limits) need a shared `lib/` module instead of route-to-route imports.
