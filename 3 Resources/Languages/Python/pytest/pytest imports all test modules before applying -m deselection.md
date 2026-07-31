---
ai_hash: 6604279d0b8a8649
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-20
entities: []
source: session 2026-06-20 appsflyer-data-connector ITs
status: seedling
tags:
- pytest
- testing
- docker
- gotcha
- markers
title: pytest imports all test modules before applying -m deselection
type: lesson
---

# pytest imports all test modules before applying -m deselection

pytest **imports every collected test module before** it applies `-m <markexpr>` deselection. Deselection happens after collection, so a module that fails to *import* breaks the run even if all its tests would have been deselected.

**Consequence for slim test images/environments:** if you build an image that runs only `pytest -m integration` but installs only a subset of deps, pytest still tries to import the unit-test modules — and any `import responses` (or other dev-only dep) raises a collection ERROR and exits non-zero. Selecting `-m integration` does NOT stop those modules from being imported.

**Fixes:** copy/include only the test subtree you intend to run (e.g. `COPY tests/integration`), OR install the deps every collected module imports, OR keep heavy/optional imports inside fixtures/functions rather than at module top level.

**Related marker facts:** a command-line `-m` overrides an ini `addopts = "-m '...'"` (last `-m` wins), so a default `addopts = "-m 'not integration'"` keeps the offline run clean while the IT image overrides with `-m integration`. Running non-root in a container also can't write `.pytest_cache` under a root-owned workdir — disable it with `-p no:cacheprovider` (e.g. via `PYTEST_ADDOPTS`).

%% ai-graph-start %%

**Related notes:**
- [[Guard tests that need an optional dependency with pytest.importorskip]]

%% ai-graph-end %%