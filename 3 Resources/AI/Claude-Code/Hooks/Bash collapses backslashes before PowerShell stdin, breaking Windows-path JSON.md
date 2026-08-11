---
ai_hash: bf560f59ef18b979
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-01
entities: []
source: sessions 2026-06-01 / 2026-06-02 (obsidian-capture tuning, hook claim-file
  testing)
status: seedling
tags:
- powershell
- bash
- windows
- json
- claude-code
- hooks
- testing
- gotcha
title: Bash collapses backslashes before PowerShell stdin, breaking Windows-path JSON
type: lesson
---

# Bash collapses backslashes before PowerShell stdin, breaking Windows-path JSON

Feeding a JSON payload containing Windows paths to a PowerShell (`.ps1`) stdin hook **from a hand-written bash string** silently corrupts it. Two layers of backslash interpretation compound: JSON needs `C:\\work\\foo.py`, but bash single-quotes + `echo`/`printf '%s'` collapse `\\` back to `\` before the bytes reach PowerShell (`\n` survives untouched). The hook receives `C:\work\foo.py`, where `\w` is an unrecognized escape and the payload is **invalid JSON**.

`ConvertFrom-Json` then throws. Because the standard safety wrapper is `trap { exit 0 }` / `try/catch` + `exit 0`, the failure is swallowed: the hook emits nothing and *looks like it simply chose not to fire*. The bug is in the test harness, not the hook - this masks the real cause and costs real debugging time.

**Fix - never build the payload as a bash string.** Either:
- generate it with Python: `python3 -c "import json,sys;sys.stdout.write(json.dumps({...}))" > payload.json`, then feed it with `< payload.json`; or
- write the file with exact bytes (editor/Write tool) and pipe it: `Get-Content "<file>" -Raw | powershell ... hook.ps1`.

`json.dumps` emits correctly-escaped backslashes that survive intact. Verify the bytes actually contain `\\` (e.g. `cat payload.json`) before trusting a negative result.

Adjacent Windows constraints: PowerShell 5.1 also needs pure-ASCII `.ps1` files and reads stdin as cp1252.

## Related

- [[PowerShell pipe appends a newline to native-command stdin, shifting any hash]]
- [[Cooperating PostToolUse hooks via a shared per-event SHA1 claim file]]

%% ai-graph-start %%

**Related notes:**
- [[PowerShell pipe appends a newline to native-command stdin, shifting any hash]]
- [[Windows Python resolves a leading-slash path to C-colon-tmp, not Git Bash tmp]]
- [[Cooperating PostToolUse hooks via a shared per-event SHA1 claim file]]
- [[Windows PowerShell 5.1 reads BOM-less scripts as ANSI, breaking on em-dashes]]
- [[PowerShell here-string @'...'@ silently corrupts git commit messages in the Bash tool]]

%% ai-graph-end %%