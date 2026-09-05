---
title: "Write-stub bank proxy: test a persistence-writing pipeline against live reads without mutating state"
created: 2026-09-03
type: howto
status: seedling
source: "session 2026-09-03 — KGA G4 e2e test"
tags: [testing, integration-test, proxy, python, live-test]
---

# Write-stub bank proxy: test a persistence-writing pipeline against live reads without mutating state

To exercise a pipeline that both READS and WRITES shared/prod state (a DB, a GCS store, an index) **end-to-end against the real live data** without mutating it, wrap the real store in a **write-stub proxy**: named write methods become **recorded no-ops**, and everything else delegates to the real object via `__getattr__`.

```python
class WriteStubBank:
    _WRITERS = {"upsert_note", "update_index", "append_run_log", "_put"}
    def __init__(self, real): self._real, self.calls = real, []
    def __getattr__(self, name):
        if name in self._WRITERS:
            def noop(*a, **k): self.calls.append((name, a, k))   # record, do nothing
            return noop
        return getattr(self._real, name)                          # readers pass through live
```

**Why this is the right tool:**
- **Reads are real** — the pipeline sees genuine live data (real index, real API responses), so the run is a true end-to-end test, not a mock.
- **Writes are contained** — shared/prod state is never mutated, so the test is safe to run against production reads.
- **The recorded write-ledger doubles as an assertion**: it *proves* no mutation happened AND shows exactly what the pipeline would have written (e.g. "40 upsert_note + 1 update_index + 1 append_run_log intercepted"), which is itself a useful behavioral check.

**Watch-outs:** enumerate ALL writers (including low-level ones like a private `_put`) or a write leaks through; `__getattr__` only fires for names not found normally, so dont also define the writers as real methods on the proxy; if reads depend on prior writes within the same run (read-your-writes), a pure write-stub diverges from real behavior — then you need an in-memory shadow instead.

Surfaced testing the test-agent KGA G4 grounding gate end-to-end (live Vertex + Atlassian + GCS index reads, zero index writes).
