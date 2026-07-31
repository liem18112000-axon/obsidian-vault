---
title: "Next.js App Router route.ts files can only export recognized handler names"
created: 2026-07-09
type: lesson
tags: [nextjs, typescript, app-router, gotcha]
---

# Next.js App Router route.ts files can only export recognized handler names

A Next.js App Router `route.ts` file can ONLY export the recognized route-handler names (`GET`, `POST`, `PUT`, `DELETE`, `PATCH`, `HEAD`, plus config exports like `runtime`/`dynamic`/`revalidate`) — exporting any OTHER named constant (a helper object, a shared type, a utility function) fails typecheck, not at the route file itself but in a generated `.next/types/app/.../route.ts` file Next.js writes, with a confusing error like `Property X is incompatible with index signature` / `does not satisfy the constraint {[x: string]: never}`.

This only surfaces when you actually run `tsc --noEmit` (or `next build`) AFTER Next has generated its route type-checking file — a plain read of the route source gives no hint anything is wrong.

Fix: keep any extra constant/helper a route needs internal to the file (drop the `export` keyword) if nothing outside the route actually imports it, or move it to a separate `lib/` module and import it into the route if it genuinely needs to be shared.
