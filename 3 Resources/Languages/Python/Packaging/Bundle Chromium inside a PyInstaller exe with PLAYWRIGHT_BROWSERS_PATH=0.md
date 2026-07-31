---
title: "Bundle Chromium inside a PyInstaller exe with PLAYWRIGHT_BROWSERS_PATH=0"
created: 2026-06-10
type: howto
status: seedling
source: "fb-info-project build workflow, session 2026-06-10"
tags: [pyinstaller, playwright, packaging, windows]
---

# Bundle Chromium inside a PyInstaller exe with PLAYWRIGHT_BROWSERS_PATH=0

Playwright resolves browsers from %LOCALAPPDATA%\ms-playwright by default, so a frozen PyInstaller app breaks on machines that never ran `playwright install`. The fix is a matched pair: at **build time**, run `playwright install chromium` with `PLAYWRIGHT_BROWSERS_PATH=0`, which installs Chromium into `site-packages/playwright/driver/package/.local-browsers` — inside the package, so `collect_all('playwright')` sweeps it (plus the Node driver) into the bundle. At **runtime**, a PyInstaller runtime hook must set the same env var so Playwright looks in the bundled location:

```python
# runtime hook
import os
os.environ.setdefault('PLAYWRIGHT_BROWSERS_PATH', '0')
```

Forgetting either half fails: without the install-time var the browser isn't in the bundle; without the runtime hook the exe looks in %LOCALAPPDATA% and finds nothing.

Use **onedir, not onefile**: the Node driver + Chromium don't survive onefile's temp extraction reliably, and a ~300 MB onefile exe re-extracts on every launch. Trade-off: the bundle is self-contained but large (~200 MB zipped).

## Related

- [[PyInstaller needs collect_all for packages that ship non-Python payloads]]
