---
ai_hash: 5c1dafd8cdb2ab71
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-10
entities: []
source: fb-info-project CI failure, session 2026-06-10
status: seedling
tags:
- pyinstaller
- packaging
- ci
- gotcha
title: PyInstaller spec paths resolve inconsistently - anchor runtime_hooks on SPECPATH
type: lesson
---

# PyInstaller spec paths resolve inconsistently - anchor runtime_hooks on SPECPATH

Inside a PyInstaller .spec file, relative paths do NOT all resolve against the same base: the `Analysis([...])` script list resolves relative to the spec file's directory, but `runtime_hooks=[...]` (and some other inputs) resolve relative to the cwd PyInstaller was invoked from. So `pyinstaller packaging/app.spec` run from the repo root finds `../scraper.py` fine yet dies with FileNotFoundError on a runtime hook sitting right next to the spec.

Fix: PyInstaller injects a `SPECPATH` global (the spec file's directory) into the spec namespace — anchor every non-script path on it:

```python
runtime_hooks=[os.path.join(SPECPATH, 'runtime_hook_playwright.py')]
```

The failure only appears when the spec lives in a subdirectory and is invoked from elsewhere (typical CI layout), so it tends to pass locally-from-the-spec-dir and break in CI.

Related: [[Bundle Chromium inside a PyInstaller exe with PLAYWRIGHT_BROWSERS_PATH=0]]

## Related

- [[Bundle Chromium inside a PyInstaller exe with PLAYWRIGHT_BROWSERS_PATH=0]]

%% ai-graph-start %%

**Related notes:**
- [[Bundle Chromium inside a PyInstaller exe with PLAYWRIGHT_BROWSERS_PATH=0]]
- [[PyInstaller needs collect_all for packages that ship non-Python payloads]]
- [[Black-box E2E test a PyInstaller one-dir app from a temp CWD]]

%% ai-graph-end %%