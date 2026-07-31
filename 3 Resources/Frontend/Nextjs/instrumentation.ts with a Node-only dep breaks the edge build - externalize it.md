---
title: "instrumentation.ts with a Node-only dep breaks the edge build - externalize it"
aliases: ["Next instrumentation.ts compiles for edge too; warm Node-only singletons via a nodejs route GET, not instrumentation"]
created: 2026-07-24
type: gotcha
status: seedling
source: "sessions 2026-07-18 (Vinnstack warm-up) / 2026-07-24 (Claude Routine smoke test)"
tags: [nextjs, instrumentation, webpack, edge-runtime, build, gotcha, vinnstack]
---

# instrumentation.ts with a Node-only dep breaks the edge build - externalize it

Next.js compiles `instrumentation.ts` for BOTH the nodejs and edge runtimes. Guarding `register()` with `if (process.env.NEXT_RUNTIME !== "nodejs") return` prevents *execution* on edge but NOT *bundling* — webpack still statically resolves the `await import()` target's whole dependency graph for the edge compilation. A Node-only transitive dep therefore breaks the build:

- `pg` → `pgpass` → `fs` ⇒ `Module not found: Can't resolve 'fs'`
- a native-backed module (onnxruntime-node translator) importing `node:os`/`node:path` ⇒ `UnhandledSchemeError: Reading from "node:os" is not handled by plugins`

Only `next build` catches it — never `tsc`, never `next dev`. **A real `next build` is part of the smoke test for anything touching instrumentation.**

**What does NOT work:** `experimental.serverComponentsExternalPackages: ['pg']` — that governs the nodejs/server-components bundle, not edge. And externalizing transitive deps one at a time is whack-a-mole (`node:child_process`, `node:fs`, `node:os` surface next).

**Fix A — externalize the dynamic-import ENTRY for edge** (verified, Vinnstack, Next 14.2.15) so webpack never walks its graph:
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
The nodejs guard means the (edge-unresolvable) external require is never reached at runtime. Externalize the ENTRY module, not each transitive dependency.

**Fix B — don't use instrumentation at all for warm-up.** To warm a native/Node-only singleton, expose a GET handler on an API route with `export const runtime = "nodejs"` and fire it fire-and-forget from a client `useEffect` on mount. The nodejs route handles node builtins fine, and "on mount" == "on app start" for a desktop/SPA. In `next dev` the first hit pays the ~15s on-demand route compile; in a prebuilt/packaged server it returns immediately and warms in the background.

Related: [[Next 14 ignores instrumentation.ts without experimental.instrumentationHook]] — sibling gotcha; you need the hook flag to run at all, then this to build.

## Related

- [[Next 14 ignores instrumentation.ts without experimental.instrumentationHook]]
- [[Offline English-Vietnamese MT for a Node-Electron app Transformers.js + Xenova opus-mt-en-vi]]
