---
ai_hash: 5d4a06188f661c7b
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-01
entities: []
source: session 2026-07-01 (Vinnstack test setup)
status: seedling
tags:
- vitest
- mocking
- vite
- nodejs
- gotcha
- testing
title: 'vi.mock of a node: builtin needs a default export too (Vite CJS interop)'
type: lesson
---

# vi.mock of a node: builtin needs a default export too (Vite CJS interop)

When a module imports a node builtin via NAMED imports (e.g. `import { existsSync } from "node:fs"`) and you `vi.mock("node:fs", () => ({...}))` in Vitest, the factory must ALSO return a `default` export — even though the source only uses named imports. Vite's CJS interop synthesizes the named bindings off the default, so a mock without `default` fails at import time with: 'No "default" export is defined on the "node:fs" mock.'

Fix: return both:
```ts
const fs = vi.hoisted(() => ({ existsSync: vi.fn(), readFileSync: vi.fn() }));
vi.mock("node:fs", () => ({ ...fs, default: fs }));
vi.mock("node:child_process", () => ({ execFile: vi.fn(), default: { execFile: vi.fn() } }));
```
Use `vi.hoisted` for the shared mock object so it exists before the hoisted `vi.mock` factory runs and you can assert on the same fns in tests.

Surfaced setting up Vitest for Vinnstack (mocking node:fs / node:child_process in config.ts tests).

%% ai-graph-start %%

**Related notes:**
- [[vi.mock factory must return every named export the SUT imports]]
- [[mockReset does not reliably restore a vi.hoisted factory implementation]]
- [[vi.clearAllMocks() does not undo a prior mockReturnValue]]
- [[vi.fn with a zero-arg default locks the mock to a zero-arg signature]]
- [[Test module-load env decisions with vi.resetModules plus dynamic import per case]]

%% ai-graph-end %%