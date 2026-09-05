---
title: "Line-removal regex must use \r?\n when reading files with newline=''"
created: 2026-08-06
type: lesson
status: seedling
source: "session 2026-08-06 luz_docs_import refactor"
tags: [python, regex, windows, crlf, gotcha]
---

# Line-removal regex must use \r?\n when reading files with newline=''

When you read a file with Python `open(path, newline="")` (to preserve the original line endings on a round-trip write), a CRLF file keeps its `\r\n`. A line-deletion regex anchored on `;\n` or `;$` then silently fails to match, because the real text is `;\r\n` — there is a `\r` between the `;` and the `\n`.

**Fix:** end line-oriented patterns with `\r?\n` (or strip `\r` first). Example that bit me: removing a Java import/constant line on Windows —

```python
s = open(p, encoding="utf-8", newline="").read()   # keeps \r\n
s = re.sub(r"[ \t]*import java\.util\.regex\.Pattern;\r?\n", "", s)  # \r? is required
```

Note the asymmetry: string `.index('\n')` / `.find('\n')` and `.replace('literal')` are CRLF-safe (they locate the \n and the surrounding \r falls inside the removed span), so brace-matched block removal worked while the line regexes did not. Only the regexes that pinned the end-of-line needed `\r?`.

Reading with the default (universal-newlines) mode would translate \r\n→\n and dodge this, but then a write re-emits \n and rewrites every line ending of the file — undesirable when you want a minimal diff. So `newline=""` + `\r?\n` is the minimal-diff combo.

## Related

- [[luz_docs_import]]
