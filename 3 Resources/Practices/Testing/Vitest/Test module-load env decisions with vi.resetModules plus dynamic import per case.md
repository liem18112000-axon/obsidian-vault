---
title: "Test module-load env decisions with vi.resetModules plus dynamic import per case"
created: 2026-07-04
type: howto
status: seedling
source: "session 2026-07-04, vinnstack middleware tests"
tags: [vitest, env, modules]
---

# Test module-load env decisions with vi.resetModules plus dynamic import per case

Code that reads env at MODULE LOAD (`const configured = !!process.env.X` at top level - common in Next.js middleware) can't be re-tested by changing `process.env` between cases: the decision is frozen at first import. Per case: set env -> `vi.resetModules()` -> `const { middleware } = await import("@/middleware")`. Restore the original env values in afterEach (save them before the suite), and also resetModules there so later suites re-import fresh.

Contrast with call-time reads (`const configured = () => !!process.env.X`): those test with plain env twiddling, no reimport. When WRITING such code, prefer call-time reads precisely because they're testable and pick up env changes - reserve module-load constants for edge runtimes (middleware) where per-request evaluation costs.

## Related

- [[vi.mock factory must return every named export the SUT imports]]
