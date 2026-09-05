---
title: "Windows Python stdout is cp1252 and crashes on emoji; reconfigure to UTF-8"
created: 2026-08-11
type: lesson
status: seedling
source: "session 2026-08-11 luz-docs-import Gap-3 testing"
tags: [python, windows, unicode, gotcha]
---

# Windows Python stdout is cp1252 and crashes on emoji; reconfigure to UTF-8

On Windows, Python's `sys.stdout` defaults to the ANSI code page (**cp1252**), not UTF-8. Printing any character outside cp1252 — most commonly emoji like ✅ or ❌ — raises `UnicodeEncodeError: 'charmap' codec can't encode character '✅'`, killing the script on its first such `print`.

Fix at program start (Python 3.7+):

```python
import sys
for s in (sys.stdout, sys.stderr):
    try:
        s.reconfigure(encoding="utf-8")
    except (AttributeError, ValueError):
        pass
```

Alternatives: set `PYTHONUTF8=1` (or `PYTHONIOENCODING=utf-8`) in the environment, or run Python 3.15+ where UTF-8 mode is default. Note a Git-Bash/MSYS pipe can still report `encoding=cp1252`, so the in-code `reconfigure` is the most robust fix for cross-shell scripts.

Bit me twice while testing luz-docs-import: the verifier `verify_gap3.py` crashed printing its ✅/❌ check marks even though the actual assertions were fine — a test-harness bug masquerading as a real failure.

## Related

- [[ZIP entry names decode as CP437 mojibake when the UTF-8 EFS flag is unset]]
