---
ai_hash: cfe78d1a5d540b09
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-23
entities: []
source: session 2026-07-23
status: seedling
tags:
- react
- lazy-import
- mermaid
- gotcha
- vinnstack
title: Cached-rejected lazy import silently breaks a feature for the whole session
type: lesson
---

# Cached-rejected lazy import silently breaks a feature for the whole session

Vinnstack's mermaid diagrams "stopped rendering" (LUZ-157741, 2026-07-23) even though every diagram PARSED CLEAN under the exact bundled mermaid 11.15.0 (verified headless via jsdom + DOM-global shims). The defect was the loader pattern, not the content:

```ts
let libPromise = null;
async function getLib() {
  if (!libPromise) libPromise = import("heavy-lib").then(init);
  return libPromise; // ← a REJECTED promise stays cached forever
}
```

One chunk-load hiccup (dev-server rebuild race, corrupted .next webpack cache, flaky network) rejects the promise once — and every later consumer awaits the same rejection until a full page reload. Compounded by `catch(() => null)` + silent `return`: no console output, no failed state, just eternally-raw fallback content. Undiagnosable by design.

Fixes (all three, not just one):
1. **Reset on rejection**: `libPromise = libPromise.catch(e => { libPromise = null; throw e; })` — next mount retries.
2. **Log the reason**: a silent catch on a render path makes failures unreportable; console.warn with the error.
3. **Distinguish loading from failed** in the UI: same fallback for both states means users can't tell a slow render from a dead one (spinner while in flight, honest caption on failure).

Diagnosis technique worth keeping: validate mermaid code headless with node + jsdom by shimming window/document/DOMParser/etc. as globals before `import("mermaid/dist/mermaid.esm.min.mjs")` — separates content bugs from runtime bugs in one command.

## Related

- [[Two next dev instances sharing one .next corrupt the webpack PackFileCache]]

%% ai-graph-start %%

**Related notes:**
- [[Mermaid render() leaks its error-bomb SVG into the DOM past a caught throw; fix with suppressErrorRendering]]
- [[A render crash masks latent crashes elsewhere in the same React subtree]]
- [[Next.js dev server webpack chunk cache corrupts after many route addsdeletes]]
- [[Async-rendered content (Mermaid) causes layout shift that misdirects form clicks]]
- [[Set HTTPserverless maxDuration above the internal LLM-run timeout, not below]]

%% ai-graph-end %%