---
title: "Windows cp1252 console crashes on non-ASCII Python prints; force UTF-8"
created: 2026-06-05
type: gotcha
status: seedling
tags: [python, windows, encoding, cp1252, argparse, gotcha]
entities: [UnicodeEncodeError, charmap, PYTHONUTF8, PYTHONIOENCODING, sys.stdout.reconfigure, "json.dumps(ensure_ascii=False)", model_dump_json, argparse]
---

# Windows cp1252 console crashes on non-ASCII Python prints; force UTF-8

The default Windows console encoding is **cp1252**, not UTF-8, so any `print()` of a character outside cp1252 dies with `UnicodeEncodeError: 'charmap' codec can't encode character`. The compute is fine — only the write to the terminal fails, so the crash lands at the *end* of an otherwise-successful run and looks unrelated.

Hits hardest with `json.dumps(..., ensure_ascii=False)` / pydantic `model_dump_json(ensure_ascii=False)`, Vietnamese/CJK text, and **argparse help strings**: `--help` crashes on `→` (U+2192) while the em-dash `—` survives (cp1252 has it at 0x97), which makes it look random — one CLI prints help fine, the next dies. The `--help` path is rarely covered by tests, so it ships broken; only actually executing every console script catches it.

**Fixes (pick one):**
- In-script, no hidden env dependency (preferred for scripts others run):
  ```python
  import sys
  sys.stdout.reconfigure(encoding="utf-8")
  ```
- Env var before running: `PYTHONUTF8=1` or `PYTHONIOENCODING=utf-8`.
- For argparse specifically, just keep help strings ASCII (`->` not `→`).

Seen in ai-trip-planner (`backend/tests/test_full_fb_crawler.py`, `Quận 6`) and leo-appsflyer-gen (post-pyproject console-script verification).

## Related

- [[Grep-audit env vars against code before pruning .env files]]
