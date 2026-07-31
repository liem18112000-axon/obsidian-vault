---
title: "Dataclass field defaults reading env vars are evaluated at import time, not instantiation"
created: 2026-07-11
type: lesson
status: seedling
source: "virtual-avatar session 2026-07-11, scripts/ingest.py + app/config.py"
tags: [python, dataclass, dotenv, import-order, gotcha]
---

# Dataclass field defaults reading env vars are evaluated at import time, not instantiation

A `@dataclass` field default like `foo: str = os.environ["FOO"]` is evaluated once, at class-definition time — i.e. the moment the module containing the dataclass is first imported — not later when the class is instantiated (`Settings()`).

This matters for anything that loads environment variables from a `.env` file (e.g. `python-dotenv`'s `load_dotenv()`): the `load_dotenv()` call must run and complete *before* the first import anywhere in the process of the module defining the dataclass. If some other import (even an indirect one, e.g. `from app.embeddings import embed_texts` which itself imports `app.config`) happens first, the `os.environ[...]` lookup already ran and raised `KeyError` — calling `load_dotenv()` afterward is too late, since Python caches modules in `sys.modules` and will not re-run the class body.

Concretely, in this codebase's `scripts/ingest.py`, the fix is ordering:
```python
from dotenv import load_dotenv
load_dotenv()

from app.embeddings import embed_texts  # this transitively imports app.config
```
not the reverse.

General lesson: treat any dataclass/module-level field default that reads an env var as *eager*, not lazy. Any environment-loading step (dotenv, secret manager fetch, etc.) must precede the first import of that module in the process — including transitive imports pulled in by unrelated-looking `import` lines.
