---
title: "Batch files must use call for activate.bat or the script stops there"
created: 2026-06-05
type: lesson
status: seedling
source: "session 2026-06-05"
tags: [windows, batch, venv, gotcha]
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
