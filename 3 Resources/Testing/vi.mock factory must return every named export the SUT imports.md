---
title: "vi.mock factory must return every named export the SUT imports"
created: 2026-07-26
type: lesson
status: seedling
source: "vinnstack cloud-build fix, session 2026-07-26"
tags: [vitest, testing, mocking, gotcha]
---

# vi.mock factory must return every named export the SUT imports

A vitest `vi.mock("@/module", () => ({ ... }))` factory must return EVERY named export that the module-under-test imports from that module — not just the ones your test asserts on. Vitest defines each returned key as a getter; accessing an export the factory omitted throws at access time:

`Error: [vitest] No "X" export is defined on the "@/module" mock. Did you forget to return it from "vi.mock"?`

This bites when the SUT gains a NEW import over time (e.g. a route starts importing `DEFAULT_EFFORT_BY_KIND`) but the older mock factory is not updated. The failure surfaces only in the code paths that touch the new export.

Key constraint: you cannot fix it by importing the real export into the factory — that loads the real module and defeats the mock. Mirror a representative literal value inline instead (a small subset is fine; the real thing is covered by that module`s own unit test).

See also [[When a merge turns CI red decide test-vs-source fix by reading code intent]].

## Related

- [[When a merge turns CI red decide test-vs-source fix by reading code intent]]
