---
ai_hash: aed4fd87f29624dd
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-10
entities: []
source: session 2026-06-10, fb-info-project test/
status: seedling
tags:
- pytest
- pyinstaller
- e2e
- packaging
- isolation
title: Black-box E2E test a PyInstaller one-dir app from a temp CWD
type: howto
---

# Black-box E2E test a PyInstaller one-dir app from a temp CWD

To E2E-test a PyInstaller **one-dir** app as a black box (exercise the shipped exe, not the source), run the exe by its ABSOLUTE path from a throwaway temp working directory, one per test. This works because of a split in how paths resolve:

- PyInstaller resolves the bundled deps (the `_internal` folder, bundled Chromium, etc.) **relative to the exe location** — so the exe still finds them no matter the CWD.
- The app's own `Path("session/...")` / `Path("output")` style relative paths resolve **relative to the process CWD**.

So each test gets fully isolated state (its own session/, output/, input files) without ever mutating the package or any real session the user dropped into it. Pair with a fixture that locates the artifact (env var -> zip in repo root -> dist/ folder) and `pytest.skip`s the whole suite when none is built, so the tests never silently pass.

Decode the exe's captured stdout/stderr as UTF-8-with-replace if it reconfigures its own console encoding.

## Related

- [[page.content() serializes the full DOM over CDP]]

%% ai-graph-start %%

**Related notes:**
- [[Black-box exe test suite skips silently when no artifact is present]]
- [[Black-box artifact E2E drive a committed sample fixture so the only live input is the secret]]
- [[Bundle Chromium inside a PyInstaller exe with PLAYWRIGHT_BROWSERS_PATH=0]]
- [[PyInstaller spec paths resolve inconsistently - anchor runtime_hooks on SPECPATH]]
- [[PyInstaller needs collect_all for packages that ship non-Python payloads]]

%% ai-graph-end %%