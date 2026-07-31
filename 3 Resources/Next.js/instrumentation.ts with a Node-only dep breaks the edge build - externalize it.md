---
title: "instrumentation.ts with a Node-only dep breaks the edge build - externalize it"
created: 2026-07-24
type: gotcha
status: seedling
source: "session 2026-07-24, Claude Routine smoke test"
tags: [nextjs, instrumentation, webpack, edge-runtime, build, gotcha]
---

# instrumentation.ts with a Node-only dep breaks the edge build - externalize it

Next.js compiles `instrumentation.ts` for BOTH the nodejs and edge runtimes. If `register()` (even via a dynamic `await import()` gated behind `process.env.NEXT_RUNTIME === 'nodejs'`) transitively pulls a **Node-only package** — e.g. `pg` → `pgpass` → `fs` — the EDGE bundle fails at build time with `Module not found: Can't resolve 'fs'`. The runtime guard prevents *execution* on edge but NOT *bundling*: webpack still statically resolves the dynamic-import target's dependency graph for the edge compilation.

Key insight: this only shows up in `next build`, never in `tsc` and never in `next dev` (dev doesn't build the edge bundle the same way). So typecheck-clean + dev-clean code can still fail the production build. **A `next build` IS part of the smoke test for anything touching instrumentation** — don't sign off on tsc + dev alone.

**What did NOT work:** adding the package to `experimental.serverComponentsExternalPackages` (e.g. `['pg']`). That governs the **nodejs/server-components** bundle, NOT the edge bundle — the edge compilation still walked pg's graph and failed. Worse, once pg resolved, the *rest* of the scheduler's node-only graph surfaced (`node:child_process`, `node:fs`, `node:os` from config.ts and the CLI-spawning runners) — externalizing transitive deps one-by-one is whack-a-mole.

**What worked (verified, Vinnstack, Next 14.2.15):** externalize the **single dynamic-import ENTRY** for the edge runtime, so webpack never walks its graph at all:
```js
webpack: (config, { nextRuntime }) => {
  if (nextRuntime === "edge") {
    config.externals = [
      ...(Array.isArray(config.externals) ? config.externals : [config.externals]),
      ({ request }, cb) =>
        request === "@/lib/routine/routineScheduler"   // the exact await import() specifier
          ? cb(null, "commonjs " + request) : cb(),
    ];
  }
  return config;
}
```
The `NEXT_RUNTIME==="nodejs"` guard in `register()` means the (unresolvable-on-edge) external require is never actually reached at runtime. Externalize the ENTRY module, not each transitive dependency.

Relates to: [[Next 14 ignores instrumentation.ts without experimental.instrumentationHook]] (the sibling gotcha — you need the hook flag to run at all, then the externalization to build).

## Related

- [[Next 14 ignores instrumentation.ts without experimental.instrumentationHook]]
