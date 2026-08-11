---
ai_hash: 6c4a0657d95c373b
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-05
entities: []
source: session 2026-06-05
status: seedling
tags:
- windows
- batch
- venv
- gotcha
title: Batch files must use call for activate.bat or the script stops there
type: lesson
---

# Batch files must use call for activate.bat or the script stops there

In a Windows `.cmd`/`.bat` script, invoking another batch file (like a venv's `activate.bat`) without `call` transfers control permanently — no line after it ever runs. Use `call <other.bat>` so control returns to the caller.

Pair it with `%~dp0` (drive + path of the script itself, with trailing backslash) so the path resolves regardless of the current working directory:

```cmd
@echo off
call "%~dp0.venv\Scripts\activate.bat"
python my_script.py
```

Without `call`, the symptom is silent: the script just exits after activation and the python line never executes — easy to misread as the python script failing instantly.

%% ai-graph-start %%

**Related notes:**
- [[winget PATH update only applies to new shells, not the current session]]
- [[spawn python ENOENT on Windows — resolve a real interpreter, not the Store alias]]
- [[Windows Python resolves a leading-slash path to C-colon-tmp, not Git Bash tmp]]

%% ai-graph-end %%