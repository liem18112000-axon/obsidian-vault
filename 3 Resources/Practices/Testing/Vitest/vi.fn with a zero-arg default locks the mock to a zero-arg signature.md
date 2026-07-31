---
ai_hash: 1be2c36493c6eb15
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-03
entities: []
source: session 2026-07-03, vinnstack test fix
status: seedling
tags:
- vitest
- typescript
- mocking
title: vi.fn with a zero-arg default locks the mock to a zero-arg signature
type: gotcha
---

# vi.fn with a zero-arg default locks the mock to a zero-arg signature

Declaring a vitest mock as `vi.fn(() => false)` makes TypeScript infer the mock's signature as `() => boolean`. Any later `mock.mockImplementation((p: string) => ...)` then fails with TS2345 ("target signature provides too few arguments") — even though it works fine at runtime, so `vitest run` stays green while `tsc --noEmit` is red.

Fix at the declaration: give the default implementation the widest parameter list you'll ever mock, e.g. `vi.fn((_p: unknown) => false)`, or type it explicitly with `vi.fn<(p: string) => boolean>()`. Mind parameter contravariance: widening the declaration to `unknown` means the `mockImplementation` call sites must also take `p: unknown` (narrow inside with `String(p)`) — a `(p: string) => ...` implementation still fails TS2345 against an `unknown`-typed mock.

Symptom to recognize: tests pass but type-check fails inside a test file on a `mockImplementation` line.

%% ai-graph-start %%

**Related notes:**
- [[vi.mock factory must return every named export the SUT imports]]
- [[vi.mock of a node builtin needs a default export too (Vite CJS interop)]]
- [[vi.clearAllMocks() does not undo a prior mockReturnValue]]
- [[mockReset does not reliably restore a vi.hoisted factory implementation]]
- [[Inferred union returns grow optional-undefined members - annotate to keep in-narrowing]]

%% ai-graph-end %%