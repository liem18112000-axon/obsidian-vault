---
ai_hash: 3607a01e24b14ea8
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-05
entities: []
source: fb-info-project session 2026-07-05
status: seedling
tags:
- testing
- ci
- pytest
- gotcha
- black-box
title: Black-box exe test suite skips silently when no artifact is present
type: lesson
---

# Black-box exe test suite skips silently when no artifact is present

A test suite designed as black-box against a built binary should decide, up front, whether a *missing* binary is a **skip** or a **failure** — because that choice silently shapes what CI actually verifies.

In fb-info-project, `test/conftest.py`'s `artifact_dir` fixture resolves the exe in order `$FB_SCRAPER_DIST` → `$FB_SCRAPER_ZIP` → repo-root `fb-scraper-windows-x64.zip` → `dist/fb-scraper/`, and if none is found it calls `pytest.skip` for the whole suite rather than failing.

**The gotcha:** a CI workflow that does *not* build the exe will still `pytest test/` green — but only the `src/`-importing `*_unit.py` tests actually ran; every black-box tier was skipped. A green check can therefore look like full coverage when the packaged-artifact tests never executed. When you remove or relocate the build step, confirm which tiers still run instead of trusting the green.

Related: [[fb-info-project CI and build workflow split]]

## Related

- [[fb-info-project CI and build workflow split]]

%% ai-graph-start %%

**Related notes:**
- [[Black-box artifact E2E drive a committed sample fixture so the only live input is the secret]]
- [[fb-info-project CI and build workflow split]]
- [[Black-box E2E test a PyInstaller one-dir app from a temp CWD]]
- [[Guard tests that need an optional dependency with pytest.importorskip]]
- [[PyInstaller needs collect_all for packages that ship non-Python payloads]]

%% ai-graph-end %%