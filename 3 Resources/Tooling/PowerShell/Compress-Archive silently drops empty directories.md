---
ai_hash: 77d051a8a57d715d
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-10
entities: []
source: fb-info-project build workflow, session 2026-06-10
status: seedling
tags:
- powershell
- ci
- packaging
- gotcha
title: Compress-Archive silently drops empty directories
type: gotcha
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

%% ai-graph-start %%

**Related notes:**
- [[Bundle Chromium inside a PyInstaller exe with PLAYWRIGHT_BROWSERS_PATH=0]]

%% ai-graph-end %%