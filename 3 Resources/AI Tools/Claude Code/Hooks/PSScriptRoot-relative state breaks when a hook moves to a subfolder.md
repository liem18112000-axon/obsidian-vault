---
title: "PSScriptRoot-relative state breaks when a hook moves to a subfolder"
created: 2026-06-02
type: lesson
status: seedling
source: "session 2026-06-02"
tags: [claude-code, hooks, powershell, gotcha, refactoring]
---

# PSScriptRoot-relative state breaks when a hook moves to a subfolder

A Claude Code hook script that locates its config/state via `$PSScriptRoot` (the script's own folder) silently changes those locations the moment you move the script into a subfolder â€” `$PSScriptRoot` becomes the subfolder, so `Join-Path $PSScriptRoot "state"` now points at `hooks/sub/state` instead of the shared `hooks/state`.

Consequences when reorganizing hooks into subfolders:
- **Config** that travels *with* the script (e.g. `simplify-gate.config.json` next to `simplify-gate.ps1`) keeps resolving via `$PSScriptRoot` â€” fine, leave it relative.
- **Shared state** does NOT travel and must not fragment. If two cooperating hooks (e.g. simplify-gate + reusable-gate) land in different subfolders, each gets its own `state/` and their shared coordination file (see [[3 Resources/AI Tools/Claude Code/Hooks/Cooperating PostToolUse hooks via a shared per-event SHA1 claim file]]) stops being shared â€” cooperation silently breaks.

Fix: point shared state at a canonical ABSOLUTE path, e.g. `Join-Path $env:USERPROFILE ".claude\hooks\state"`, so it is stable regardless of which subfolder the script lives in. Rule of thumb: per-script private files â†’ `$PSScriptRoot`-relative; cross-script/shared files â†’ absolute canonical path.

Related: [[3 Resources/AI Tools/Claude Code/Hooks/Hook files referenced by external skills via hardcoded paths cannot be relocated]].

## Related

- [[3 Resources/AI Tools/Claude Code/Hooks/Cooperating PostToolUse hooks via a shared per-event SHA1 claim file]]
- [[3 Resources/AI Tools/Claude Code/Hooks/Hook files referenced by external skills via hardcoded paths cannot be relocated]]

