---
ai_hash: 2e9ba2a59eb2a54f
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-01
entities: []
source: vault-graph refactor 2026-06-01
status: seedling
tags:
- python
- paths
- refactor
- gotcha
title: Path(__file__).parent breaks when a module is moved to a deeper directory
type: gotcha
---

# Path(__file__).parent breaks when a module is moved to a deeper directory

When a module locates a sibling file via `Path(__file__).resolve().parent` (a common pattern for loading a co-located `.env`, config, or data file), **moving that module to a different directory depth silently breaks the path** — the code still runs, it just resolves to the wrong (often nonexistent) location, so the file is quietly not found.

The fix is to re-count the `.parent` hops for the new depth. Example: `vertex_client.py` moved from `tools/vault-graph/` into `tools/vault-graph/src/` (one level deeper). The `.env` it loads lives at `tools/vault-graph/.env`, so:

```python
_HERE = Path(__file__).resolve().parent      # was tools/vault-graph/, now tools/vault-graph/src/
load_dotenv(_HERE / ".env")                  # before: tools/vault-graph/.env  ✓
load_dotenv(_HERE.parent / ".env")           # after:  add one .parent to climb back  ✓
```

`load_dotenv` (and many loaders) **no-op silently on a missing file**, so there's no error to catch — the symptom is "config not applied," which is easy to miss. Audit every `__file__`-relative path whenever you relocate a module. Related: [[Flat-import Python modules can be relocated together without rewriting imports]].

## Related

- [[Flat-import Python modules can be relocated together without rewriting imports]]

%% ai-graph-start %%

**Related notes:**
- [[Flat-import Python modules can be relocated together without rewriting imports]]
- [[Dataclass field defaults reading env vars are evaluated at import time, not instantiation]]
- [[Docker Compose path resolution env_file vs build context vs dockerfile]]
- [[Module-level load_dotenv lets unit tests hit real cloud credentials]]

%% ai-graph-end %%