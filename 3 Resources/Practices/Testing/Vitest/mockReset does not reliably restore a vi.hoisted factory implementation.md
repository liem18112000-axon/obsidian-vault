---
ai_hash: 1b2b5bfa1c471949
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-09
entities: []
source: vinnstack session 2026-07-08 (electron/main.js test coverage)
status: seedling
tags:
- vitest
- mocking
- gotcha
- testing
title: mockReset does not reliably restore a vi.hoisted factory implementation
type: lesson
---

# mockReset does not reliably restore a vi.hoisted factory implementation

In Vitest, `mockFn.mockReset()` (and `vi.resetAllMocks()`) resets a mock further than expected: it clears call history AND removes any implementation set via `mockImplementation`/`mockReturnValue`, reverting to a bare stub that returns `undefined` — INCLUDING the original factory function a mock was created with via `vi.fn(() => defaultValue)` inside `vi.hoisted(...)`.

This is surprising because `vi.hoisted(() => ({ fn: vi.fn(() => defaultValue) }))` reads as "this mock always defaults to `defaultValue`" — but that default is really just the FIRST implementation, and `mockReset()`/`resetAllMocks()` strips it just like any later override. After a reset, the mock returns `undefined` until something calls `.mockReturnValue(...)` again.

Practical effect: don`t reach for `resetAllMocks()` in a `beforeEach` expecting it to restore each mocks original hoisted default — it does the opposite (wipes it to nothing). Combined with [[vi.clearAllMocks() does not undo a prior mockReturnValue]] (which under-clears) and this (which over-clears), the only reliable `beforeEach` pattern is: call `clearAllMocks()` for call-history hygiene, then explicitly restate every mocks intended default value yourself — never rely on either reset function to do it for you.

## Related

- [[vi.clearAllMocks() does not undo a prior mockReturnValue]]

%% ai-graph-start %%

**Related notes:**
- [[vi.clearAllMocks() does not undo a prior mockReturnValue]]
- [[vi.mock factory must return every named export the SUT imports]]
- [[vi.mock of a node builtin needs a default export too (Vite CJS interop)]]
- [[Test module-load env decisions with vi.resetModules plus dynamic import per case]]
- [[vi.fn with a zero-arg default locks the mock to a zero-arg signature]]

%% ai-graph-end %%