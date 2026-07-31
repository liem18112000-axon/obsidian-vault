---
ai_hash: 92e4e719577d3949
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-03
entities: []
source: vinnstack 2026-07-03 subject generalization; vinnstack cloud-build fix 2026-07-26
status: seedling
tags:
- vitest
- testing
- mocking
- typescript
- gotcha
title: vi.mock factory must return every named export the SUT imports
type: lesson
updated: 2026-07-26
---

# vi.mock factory must return every named export the SUT imports

`vi.mock("@/module", () => ({ ... }))` replaces the **whole** module. Vitest defines each returned key as a getter, so the moment the module-under-test imports an export the factory omitted, it throws at access time:

`Error: [vitest] No "X" export is defined on the "@/module" mock. Did you forget to return it from "vi.mock"?`

Why it bites: the factory is written once while the SUT keeps gaining imports (a new constant like `DEFAULT_EFFORT_BY_KIND`, a tiny pure type-guard). Nothing checks the factory against the SUT's import list, so the failure lands later — either only on the code paths touching the new export, or at **collection** time (file reports 0 tests) with the error pointing at the *production* file's import line.

**Fixes, best first**
- **Partial mock** — `vi.mock("m", async (importOriginal) => ({ ...(await importOriginal()), justTheFn: vi.fn() }))`. Future exports keep working automatically; best for broad modules.
- **Real (or inline-copied) implementation for pure helpers** — validators, formatters — so validation behavior in the route under test stays honest.
- **Mirror a representative literal inline** for data constants. Do NOT `import` the real export inside the factory: that loads the real module and defeats the mock. A small subset value is fine; the real thing is covered by that module's own unit test.

Adjacent churn from the same change: adding an optional trailing parameter to a mocked function makes `toHaveBeenCalledWith(a, b, c)` fail because the call site now passes `undefined` as a 4th arg — assert the explicit `undefined`.

Node builtins are a special case: the factory must ALSO return `default` — see [[vi.mock of a node builtin needs a default export too (Vite CJS interop)]].

## Related

- [[vi.mock of a node builtin needs a default export too (Vite CJS interop)]]
- [[vi.fn with a zero-arg default locks the mock to a zero-arg signature]]
- [[When a merge turns CI red decide test-vs-source fix by reading code intent]]

%% ai-graph-start %%

**Related notes:**
- [[vi.mock of a node builtin needs a default export too (Vite CJS interop)]]
- [[vi.fn with a zero-arg default locks the mock to a zero-arg signature]]
- [[mockReset does not reliably restore a vi.hoisted factory implementation]]
- [[vi.clearAllMocks() does not undo a prior mockReturnValue]]
- [[Test module-load env decisions with vi.resetModules plus dynamic import per case]]

%% ai-graph-end %%