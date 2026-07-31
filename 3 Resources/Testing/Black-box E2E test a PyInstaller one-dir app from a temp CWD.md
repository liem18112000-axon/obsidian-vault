---
title: "Black-box E2E test a PyInstaller one-dir app from a temp CWD"
created: 2026-06-10
type: howto
status: seedling
source: "session 2026-06-10, fb-info-project test/"
tags: [pytest, pyinstaller, e2e, packaging, isolation]
---

# Black-box E2E test a PyInstaller one-dir app from a temp CWD

To E2E-test a PyInstaller **one-dir** app as a black box (exercise the shipped exe, not the source), run the exe by its ABSOLUTE path from a throwaway temp working directory, one per test. This works because of a split in how paths resolve:

- PyInstaller resolves the bundled deps (the `_internal` folder, bundled Chromium, etc.) **relative to the exe location** — so the exe still finds them no matter the CWD.
- The app's own `Path("session/...")` / `Path("output")` style relative paths resolve **relative to the process CWD**.

So each test gets fully isolated state (its own session/, output/, input files) without ever mutating the package or any real session the user dropped into it. Pair with a fixture that locates the artifact (env var -> zip in repo root -> dist/ folder) and `pytest.skip`s the whole suite when none is built, so the tests never silently pass.

Decode the exe's captured stdout/stderr as UTF-8-with-replace if it reconfigures its own console encoding.

## Related

- [[page.content() serializes the full DOM over CDP]]
