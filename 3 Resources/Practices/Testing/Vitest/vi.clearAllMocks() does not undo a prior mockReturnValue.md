---
ai_hash: 315dfa65c9af860c
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-09
entities: []
source: vinnstack session 2026-07-08/09
status: seedling
tags:
- vitest
- mocking
- gotcha
- testing
title: vi.clearAllMocks() does not undo a prior mockReturnValue
type: lesson
---

# vi.clearAllMocks() does not undo a prior mockReturnValue

In Vitest, `vi.clearAllMocks()` called in `beforeEach` only clears call history (`mock.calls`, `mock.results`) on every mock — it does NOT undo a `mockReturnValue(...)` / `mockResolvedValue(...)` / `mockImplementation(...)` a PREVIOUS test set. If test A calls `someMock.mockReturnValue(valueA)` and test B never overrides it itself, test B still observes `valueA`.

This bites hardest with the common pattern `vi.hoisted(() => ({ fn: vi.fn(() => defaultValue) }))` + `vi.mock("module", () => hoistedObj)` — used throughout API-route tests to mock a lib module so the route handler is tested for HTTP behavior only. The FIRST test that overrides a mocks return value silently poisons every later test in the same file that assumes the default, unless something re-establishes it.

`vi.resetAllMocks()` is not the fix either: per [[mockReset does not reliably restore a vi.hoisted factory implementation]], it wipes the mock back to a bare stub returning undefined, including the ORIGINAL factory implementation from `vi.hoisted`.

The pattern that actually works: explicitly re-set every mocks default return/resolved value at the TOP of every `beforeEach`, right after `clearAllMocks()`:

```ts
beforeEach(() => {
  vi.clearAllMocks();
  mockA.mockReturnValue(defaultA);
  mockB.mockResolvedValue(defaultB);
});
```

Treat `clearAllMocks()` as clearing call history ONLY — never assume it restores a default implementation, whether that default came from `vi.hoisted`s factory or a prior tests override.

## Related

- [[mockReset does not reliably restore a vi.hoisted factory implementation]]

%% ai-graph-start %%

**Related notes:**
- [[mockReset does not reliably restore a vi.hoisted factory implementation]]
- [[vi.mock factory must return every named export the SUT imports]]
- [[vi.mock of a node builtin needs a default export too (Vite CJS interop)]]
- [[Test module-load env decisions with vi.resetModules plus dynamic import per case]]
- [[vi.fn with a zero-arg default locks the mock to a zero-arg signature]]

%% ai-graph-end %%