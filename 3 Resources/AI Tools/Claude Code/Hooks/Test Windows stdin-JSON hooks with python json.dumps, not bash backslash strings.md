---
title: "Test Windows stdin-JSON hooks with python json.dumps, not bash backslash strings"
created: 2026-06-01
type: lesson
status: seedling
source: "session 2026-06-01 obsidian-capture tuning"
tags: [windows, powershell, json, claude-code, hooks, testing, gotcha]
---

# Test Windows stdin-JSON hooks with python json.dumps, not bash backslash strings

When testing a Windows hook (PowerShell `.ps1`) that reads a JSON payload from **stdin**, generate the payload with Python `json.dumps` and pipe a file, **not** a hand-written bash string. A Windows path like `C:\work\foo.py` needs doubled backslashes in JSON (`C:\work\foo.py`), but bash single-quotes + `printf '%s'`/`echo` collapse `\` back to `\` before the bytes reach PowerShell. The hook then receives `C:\work\foo.py`, `ConvertFrom-Json` fails on the invalid `\w` escape, and a well-behaved hook `try/catch`es it and `exit 0`s **silently** - so the hook looks broken (emits nothing) when the code is actually fine.

**Why:** the bug is in the test harness, not the hook. Two layers of backslash interpretation (bash, then JSON) compound.

**How to apply:** build the payload with `python3 -c "import json,sys;sys.stdout.write(json.dumps({...}))"` (or write a real `.json` file) and feed it via `< payload.json`. `json.dumps` emits correctly-escaped backslashes that survive intact. Verify the bytes actually contain `\` (e.g. `cat payload.json`) before trusting a negative result.

See [[feedback_no_ascii_box_diagrams]]-adjacent Windows gotchas: PowerShell 5.1 also needs pure-ASCII `.ps1` files and reads stdin as cp1252.
