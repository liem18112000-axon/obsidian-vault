---
title: "Idempotent env-file key upsert via anchored regex substitution"
created: 2026-07-11
type: technique
status: seedling
source: "virtual-avatar session 2026-07-11, scripts/set_dev_session_token.py"
tags: [python, regex, dotenv, env-file, idempotent]
---

# Idempotent env-file key upsert via anchored regex substitution

When a script needs to set/update a single `KEY=value` line in a `.env`-style file without disturbing the other lines (comments, unrelated vars), the simplest idempotent approach is an anchored regex substitution with a fallback append:

```python
import re
text = path.read_text()
new_text, count = re.subn(r"(?m)^KEY=.*$", "KEY=newvalue", text)
if count == 0:
    new_text = text.rstrip("\n") + "\nKEY=newvalue\n"
path.write_text(new_text)
```

The `(?m)^KEY=.*$` pattern anchors to line start/end (multiline mode), so it only replaces an existing `KEY=...` line in place — preserving line order, comments, and every other variable. If the key is not present yet, `re.subn` returns a count of 0, so you fall back to appending a fresh line.

This is safer than blindly overwriting the whole file (which would wipe other required variables) and safer than naive string `.replace()` (which could accidentally match a value substring elsewhere in the file). Useful for any small dev-utility script that needs to inject/update one `.env` entry — e.g. seeding a placeholder `SESSION_TOKEN=dev-token` for local runs without touching `GCP_PROJECT_ID` etc.

## Related

- [[Dataclass field defaults reading env vars are evaluated at import time]]
- [[not instantiation]]
