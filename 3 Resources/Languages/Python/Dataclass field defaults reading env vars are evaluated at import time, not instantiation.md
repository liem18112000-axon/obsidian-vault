---
ai_hash: f0853bfee4db29c1
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-11
entities: []
source: virtual-avatar session 2026-07-11, scripts/ingest.py + app/config.py
status: seedling
tags:
- python
- dataclass
- dotenv
- import-order
- gotcha
title: Dataclass field defaults reading env vars are evaluated at import time, not
  instantiation
type: lesson
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

%% ai-graph-start %%

**Related notes:**
- [[Module-level load_dotenv lets unit tests hit real cloud credentials]]
- [[Path(__file__).parent breaks when a module is moved to a deeper directory]]
- [[Idempotent env-file key upsert via anchored regex substitution]]
- [[Grep-audit env vars against code before pruning .env files]]
- [[LEO CDP SYSTEM_ENV_VARS still requires database-configs.json to exist first]]

%% ai-graph-end %%