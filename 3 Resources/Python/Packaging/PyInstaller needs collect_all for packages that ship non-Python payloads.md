---
title: "PyInstaller needs collect_all for packages that ship non-Python payloads"
created: 2026-06-10
type: lesson
status: seedling
source: "fb-info-project build workflow, session 2026-06-10"
tags: [pyinstaller, packaging, gotcha]
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
