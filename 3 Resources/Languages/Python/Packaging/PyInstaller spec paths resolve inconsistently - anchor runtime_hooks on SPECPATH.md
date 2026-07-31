---
title: "PyInstaller spec paths resolve inconsistently - anchor runtime_hooks on SPECPATH"
created: 2026-06-10
type: lesson
status: seedling
source: "fb-info-project CI failure, session 2026-06-10"
tags: [pyinstaller, packaging, ci, gotcha]
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
