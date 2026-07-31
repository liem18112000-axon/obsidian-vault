---
title: "Dual-mode CMD wrapper - args pass through, no args opens an interactive menu"
created: 2026-07-04
type: howto
status: seedling
source: "session 2026-07-04 fb-info-project license_admin.cmd"
tags: [cmd, batch, windows, operator-tooling]
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

Related: [[CMD set /p keeps the existing value on empty Enter - preset the default]]
