---
ai_hash: a9a013a119771706
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-04
entities: []
source: session 2026-07-04, vinnstack middleware tests
status: seedling
tags:
- vitest
- env
- modules
title: Test module-load env decisions with vi.resetModules plus dynamic import per
  case
type: howto
---

# Test module-load env decisions with vi.resetModules plus dynamic import per case

Code that reads env at MODULE LOAD (`const configured = !!process.env.X` at top level - common in Next.js middleware) can't be re-tested by changing `process.env` between cases: the decision is frozen at first import. Per case: set env -> `vi.resetModules()` -> `const { middleware } = await import("@/middleware")`. Restore the original env values in afterEach (save them before the suite), and also resetModules there so later suites re-import fresh.

Contrast with call-time reads (`const configured = () => !!process.env.X`): those test with plain env twiddling, no reimport. When WRITING such code, prefer call-time reads precisely because they're testable and pick up env changes - reserve module-load constants for edge runtimes (middleware) where per-request evaluation costs.

## Related

- [[vi.mock factory must return every named export the SUT imports]]

%% ai-graph-start %%

**Related notes:**
- [[vi.mock factory must return every named export the SUT imports]]
- [[vi.clearAllMocks() does not undo a prior mockReturnValue]]
- [[mockReset does not reliably restore a vi.hoisted factory implementation]]
- [[vi.mock of a node builtin needs a default export too (Vite CJS interop)]]
- [[Config read into a module-level const applies only on next process launch]]

%% ai-graph-end %%