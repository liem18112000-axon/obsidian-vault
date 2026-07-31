---
title: "Next instrumentation.ts compiles for edge too; warm Node-only singletons via a nodejs route GET, not instrumentation"
created: 2026-07-18
type: lesson
status: seedling
source: "Vinnstack session 2026-07-18"
tags: [nextjs, instrumentation, edge-runtime, onnxruntime, gotcha, vinnstack]
---

# Next instrumentation.ts compiles for edge too; warm Node-only singletons via a nodejs route GET, not instrumentation

Next.js `instrumentation.ts` register() compiles for BOTH the nodejs AND edge runtimes, even when you guard the body with `if (process.env.NEXT_RUNTIME !== "nodejs") return`. Webpack still traces a dynamic `import()` inside register() and bundles the target for the edge runtime. If that module (transitively) imports a `node:` builtin (e.g. `node:os`, `node:path` in a native-backed module like the onnxruntime-node translator), the edge compile fails with `UnhandledSchemeError: Reading from "node:os" is not handled by plugins`.

Fix used in Vinnstack (2026-07): do NOT warm a native/Node-only singleton via instrumentation.ts. Instead expose a GET handler on the relevant `export const runtime = "nodejs"` API route that calls the warm function, and fire it fire-and-forget from a client component on mount (a useEffect that does `fetch("/api/...")`). The nodejs route handles node builtins fine, and "on mount" == "on app start" for a desktop/SPA. Bonus: in `next dev` the first hit to that route pays the on-demand route-compile (~15s) so the GET looks slow, but in the packaged/prebuilt server it returns immediately and warms in the background.

Related: [[Offline English-Vietnamese MT for a Node-Electron app Transformers.js + Xenova opus-mt-en-vi]].
