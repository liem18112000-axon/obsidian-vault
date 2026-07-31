---
title: "vi.mock module factories break when prod code imports a new export"
created: 2026-07-03
type: gotcha
status: seedling
source: "session 2026-07-03, vinnstack subject generalization"
tags: [vitest, mocking, typescript]
---

# vi.mock module factories break when prod code imports a new export

`vi.mock("module", () => factory)` replaces the WHOLE module. The moment production code adds one more import from that module - even a tiny pure helper like a type guard - every existing test whose factory doesn't list it fails with `[vitest] No "x" export is defined on the mock`.

Two fixes:
- For pure helpers (validators, formatters): put the REAL implementation (or an inline copy) in the factory, so validation behavior in the route under test stays honest.
- For broad modules: `vi.mock("m", async (importOriginal) => ({ ...(await importOriginal()), justTheFn: vi.fn() }))` - partial mock keeps future exports working automatically.

Related churn source in the same change: adding an optional trailing parameter to a mocked function makes `toHaveBeenCalledWith(a, b, c)` fail because the call site now passes `undefined` as the 4th arg - assert the explicit `undefined`.

Node-builtin variant: mocking `node:fs` / `node:child_process` needs the factory to ALSO provide `default` (some transitive importer uses the default import) - `const m = { execFile: vi.fn() }; return { ...m, default: m };`. Symptom: the test file fails at COLLECTION with 0 tests and `No "default" export is defined on the mock`, pointing at the PRODUCTION file's import line.

## Related

- [[vi.fn with a zero-arg default locks the mock to a zero-arg signature]]
