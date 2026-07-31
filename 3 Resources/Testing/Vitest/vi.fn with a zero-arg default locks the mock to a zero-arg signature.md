---
title: "vi.fn with a zero-arg default locks the mock to a zero-arg signature"
created: 2026-07-03
type: gotcha
status: seedling
source: "session 2026-07-03, vinnstack test fix"
tags: [vitest, typescript, mocking]
---

# vi.fn with a zero-arg default locks the mock to a zero-arg signature

Declaring a vitest mock as `vi.fn(() => false)` makes TypeScript infer the mock's signature as `() => boolean`. Any later `mock.mockImplementation((p: string) => ...)` then fails with TS2345 ("target signature provides too few arguments") — even though it works fine at runtime, so `vitest run` stays green while `tsc --noEmit` is red.

Fix at the declaration: give the default implementation the widest parameter list you'll ever mock, e.g. `vi.fn((_p: unknown) => false)`, or type it explicitly with `vi.fn<(p: string) => boolean>()`. Mind parameter contravariance: widening the declaration to `unknown` means the `mockImplementation` call sites must also take `p: unknown` (narrow inside with `String(p)`) — a `(p: string) => ...` implementation still fails TS2345 against an `unknown`-typed mock.

Symptom to recognize: tests pass but type-check fails inside a test file on a `mockImplementation` line.
