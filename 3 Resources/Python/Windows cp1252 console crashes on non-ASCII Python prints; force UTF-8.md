---
title: "Windows cp1252 console crashes on non-ASCII Python prints; force UTF-8"
created: 2026-06-05
type: gotcha
status: seedling
source: "session 2026-06-05"
tags: [python, windows, encoding, gotcha]
---

# Windows cp1252 console crashes on non-ASCII Python prints; force UTF-8

On Windows, a Python script that prints non-ASCII text (e.g. Vietnamese `Quận 6`) crashes with `UnicodeEncodeError: 'charmap' codec can't encode character` — because the default console encoding is **cp1252**, not UTF-8. This bites hardest with `json.dumps(..., ensure_ascii=False)` or pydantic `model_dump_json(ensure_ascii=False)`, where non-ASCII is intentionally kept raw.

The fetch/parse logic is fine; only the `print()` to the terminal fails. So the bug surfaces *at the end* of an otherwise-successful run, which is misleading.

**Fixes (pick one):**
- Set an env var before running: `PYTHONUTF8=1` or `PYTHONIOENCODING=utf-8`.
- Make the script self-sufficient: add at the top
  ```python
  import sys
  sys.stdout.reconfigure(encoding="utf-8")
  ```

Prefer the in-script `reconfigure` for scripts meant to be run by others on Windows — it removes the hidden env-var dependency.

Discovered test-running `backend/tests/test_full_fb_crawler.py` in the ai-trip-planner project.
