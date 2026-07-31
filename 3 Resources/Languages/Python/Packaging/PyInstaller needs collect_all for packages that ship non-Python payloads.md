---
ai_hash: 8bda668ae7f443f7
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-10
entities: []
source: fb-info-project build workflow, session 2026-06-10
status: seedling
tags:
- pyinstaller
- packaging
- gotcha
title: PyInstaller needs collect_all for packages that ship non-Python payloads
type: lesson
---

# PyInstaller needs collect_all for packages that ship non-Python payloads

PyInstaller's import scan only follows Python imports, so packages whose real payload is data or native binaries arrive broken in the frozen app even though the import works at analysis time. For each such package, call `collect_all('<pkg>')` in the .spec and feed the three results into `Analysis(datas=, binaries=, hiddenimports=)`.

Encountered concretely in the fb-info-project Windows build:
- `playwright` — Node driver executable + browser registry
- `scrapling` — stealth/fingerprint data files
- `browserforge` / `apify_fingerprint_datapoints` — fingerprint JSON datasets
- `curl_cffi` — libcurl DLLs + CA bundle

Symptom when missed: the exe builds fine, then fails at runtime with FileNotFoundError/missing-DLL errors deep inside the library — test the frozen exe, not just the build.

See [[Bundle Chromium inside a PyInstaller exe with PLAYWRIGHT_BROWSERS_PATH=0]] for the Playwright-specific browser half.

## Related

- [[Bundle Chromium inside a PyInstaller exe with PLAYWRIGHT_BROWSERS_PATH=0]]

%% ai-graph-start %%

**Related notes:**
- [[Bundle Chromium inside a PyInstaller exe with PLAYWRIGHT_BROWSERS_PATH=0]]
- [[PyInstaller spec paths resolve inconsistently - anchor runtime_hooks on SPECPATH]]
- [[scrapling[fetchers] extra pins playwright exactly - install deps individually to keep your own pin]]
- [[Black-box E2E test a PyInstaller one-dir app from a temp CWD]]
- [[Black-box exe test suite skips silently when no artifact is present]]

%% ai-graph-end %%