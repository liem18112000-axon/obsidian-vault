---
title: "Compress-Archive silently drops empty directories"
created: 2026-06-10
type: gotcha
status: seedling
source: "fb-info-project build workflow, session 2026-06-10"
tags: [powershell, ci, packaging, gotcha]
---

# Compress-Archive silently drops empty directories

PowerShell's `Compress-Archive` omits empty directories from the zip, so a staged folder layout that relies on empty dirs (e.g. a `session/` or `output/` folder a tool expects to exist) arrives incomplete on the user's machine. Drop a placeholder file into each intentionally-empty dir before zipping — a one-line README.txt doubles as user instructions:

```powershell
New-Item -ItemType Directory -Force dist/app/session | Out-Null
Set-Content -Encoding utf8 dist/app/session/README.txt 'Put fb_session.json here.'
```

Same caveat applies to git (doesn't track empty dirs) — the .gitkeep convention is the analogous fix.

## Related

- [[Bundle Chromium inside a PyInstaller exe with PLAYWRIGHT_BROWSERS_PATH=0]]
