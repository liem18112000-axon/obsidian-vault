---
ai_hash: 80118cec48aa59bc
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-31
entities: []
source: vault PARA reorganization 2026-07-31
status: seedling
tags:
- python
- windows
- encoding
- gotcha
title: A charmap UnicodeEncodeError can kill a script before it writes its output
  file
type: gotcha
---

# A charmap UnicodeEncodeError can kill a script before it writes its output file

Python on Windows encodes stdout with the console codepage (cp1252), so printing any non-ASCII character raises `UnicodeEncodeError: 'charmap' codec can't encode character`. That is well known. The part that wastes time is the **ordering consequence**.

If the script prints a report and *then* writes its output file, the print crashes first and the file is never written. From outside, the write looks like the thing that silently failed — you go debugging file paths and permissions when the real cause is one Vietnamese title in a diagnostic line.

I lost two debugging rounds to exactly this: a link-analysis script printed a broken-link table containing `\u1ec7`, died, and never reached its `json.dump`.

Two defences, use both:

```python
import sys
sys.stdout.reconfigure(encoding='utf-8', errors='replace')
```

and **write data files before printing human output**, so a formatting crash can never cost you the results. Redirecting to a file does not save you — the encoding is chosen from the environment, not the destination. `PYTHONIOENCODING=utf-8` works as an external override.

## Related

- [[Windows Python resolves a leading-slash path to C-colon-tmp, not Git Bash tmp]]

%% ai-graph-start %%

**Related notes:**
- [[Windows cp1252 console crashes on non-ASCII Python prints; force UTF-8]]
- [[Windows Python resolves a leading-slash path to C-colon-tmp, not Git Bash tmp]]
- [[Bash collapses backslashes before PowerShell stdin, breaking Windows-path JSON]]
- [[Windows PowerShell 5.1 reads BOM-less scripts as ANSI, breaking on em-dashes]]

%% ai-graph-end %%