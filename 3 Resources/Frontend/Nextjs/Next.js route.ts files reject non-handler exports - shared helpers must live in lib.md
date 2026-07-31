---
title: "Next.js route.ts files reject non-handler exports - shared helpers must live in lib"
created: 2026-07-03
type: lesson
status: seedling
source: "session 2026-07-03, vinnstack uploads refactor"
tags: [nextjs, app-router, typescript]
---

# Next.js route.ts files reject non-handler exports - shared helpers must live in lib

In the Next.js App Router, a `route.ts` file may only export the route handlers (`GET`, `POST`, ...) and route-config fields (`runtime`, `dynamic`, ...). Exporting an arbitrary helper or constant from a route file fails the build's route type-check.

Consequence: when two API routes need the same helper (e.g. a `safeUploadName` filename sanitizer and upload size limits), you cannot export it from one route and import it in the other — extract it to a module under `lib/` and import it in both. This is the structural reason "shared upload guards" files exist rather than route-to-route imports.
