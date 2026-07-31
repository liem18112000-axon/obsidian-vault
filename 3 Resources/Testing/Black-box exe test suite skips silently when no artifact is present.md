---
title: "Black-box exe test suite skips silently when no artifact is present"
created: 2026-07-05
type: lesson
status: seedling
source: "fb-info-project session 2026-07-05"
tags: [testing, ci, pytest, gotcha, black-box]
---

# Black-box exe test suite skips silently when no artifact is present

A test suite designed as black-box against a built binary should decide, up front, whether a *missing* binary is a **skip** or a **failure** — because that choice silently shapes what CI actually verifies.

In fb-info-project, `test/conftest.py`'s `artifact_dir` fixture resolves the exe in order `$FB_SCRAPER_DIST` → `$FB_SCRAPER_ZIP` → repo-root `fb-scraper-windows-x64.zip` → `dist/fb-scraper/`, and if none is found it calls `pytest.skip` for the whole suite rather than failing.

**The gotcha:** a CI workflow that does *not* build the exe will still `pytest test/` green — but only the `src/`-importing `*_unit.py` tests actually ran; every black-box tier was skipped. A green check can therefore look like full coverage when the packaged-artifact tests never executed. When you remove or relocate the build step, confirm which tiers still run instead of trusting the green.

Related: [[fb-info-project CI and build workflow split]]

## Related

- [[fb-info-project CI and build workflow split]]
