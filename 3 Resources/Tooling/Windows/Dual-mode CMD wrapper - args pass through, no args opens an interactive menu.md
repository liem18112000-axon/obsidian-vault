---
ai_hash: 4894aed8f52995ba
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-04
entities: []
source: session 2026-07-04 fb-info-project license_admin.cmd
status: seedling
tags:
- cmd
- batch
- windows
- operator-tooling
title: Dual-mode CMD wrapper - args pass through, no args opens an interactive menu
type: howto
---

# Dual-mode CMD wrapper - args pass through, no args opens an interactive menu

A single .cmd file can serve both developers (terminal) and non-developers (double-click) by branching on whether arguments were given, at the very top:

```bat
if not "%~1"=="" (
    python tools\real_tool.py %*
    exit /b %errorlevel%
)
REM no args -> interactive menu
choice /c 12 /n /m "Pick [1-2]: "
if errorlevel 2 goto sign
...
```

- With args: the wrapper is a thin pass-through to the real tool, propagating its exit code (`exit /b %errorlevel%`).
- Without args (the double-click case): it falls into a `choice`-driven menu plus `set /p` prompts, then ends with `pause` so the console window does not vanish before the operator can copy the output.

Useful for operator tooling handed to non-technical users while keeping full CLI flexibility for yourself. Example: tools\license_admin.cmd in fb-info-project wrapping an Ed25519 license keygen/sign script.

Related: [[3 Resources/Tooling/Windows/CMD set p keeps the existing value on empty Enter - preset the default]]

%% ai-graph-start %%

**Related notes:**
- [[CMD set p keeps the existing value on empty Enter - preset the default]]

%% ai-graph-end %%