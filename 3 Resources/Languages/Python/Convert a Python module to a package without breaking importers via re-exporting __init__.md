---
ai_hash: 54c511480411e572
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-03
entities: []
source: session 2026-07-03 appsflyer-data-connector
status: seedling
tags:
- python
- refactoring
- packaging
title: Convert a Python module to a package without breaking importers via re-exporting
  __init__
type: howto
---

# Convert a Python module to a package without breaking importers via re-exporting __init__

Splitting a grown Python module (e.g. `sink.py`) into a package is safe for every caller if the new `sink/__init__.py` re-exports the full public surface: `from .cdp_http import CdpHttpSink`, one line per moved name, plus `__all__`. Python resolves `from pkg.sink import X` identically whether `sink` is a module or a package, so all import sites keep working verbatim — zero caller edits.

Recipe:
1. Grep all import sites first (`from .*sink import`) to know the exact public surface to preserve.
2. Move each class verbatim into its own module (`base.py` for the Protocol, one file per implementation, `factory.py` for env-routed builders). Relative imports gain one level (`.envelope` → `..envelope`).
3. `__init__.py` = package docstring + re-exports + `__all__`. Nothing else.
4. Delete the old `.py` file in the same change — a `sink.py` and `sink/` sibling is ambiguous.
5. Run the full test suite; a pure 1:1 move should be green with no test edits (155/155 in appsflyer-data-connector).

One breakage class survives the re-export trick — see [[Monkeypatched module attributes are a hidden breakage risk when a module becomes a package]].

## Related

- [[Monkeypatched module attributes are a hidden breakage risk when a module becomes a package]]

**When the goal is relocation, not splitting:** if modules are being *moved to a new home* (e.g. `common/config.py` + `common/envelope.py` → `common/models/`), a compatibility re-export at the old path is the wrong tool — it leaves two import paths alive forever. Rewrite every import site instead (grep-audit first, then anchored `sed` across src/tests/examples/docs), and let the new package's `__init__.py` re-export only at the NEW path.

%% ai-graph-start %%

**Related notes:**
- [[Monkeypatched module attributes are a hidden breakage risk when a module becomes a package]]
- [[Extracting a shared utils package - classify by whether code knows source semantics]]
- [[AppsFlyer package layout package-per-concern with no loose modules]]
- [[Flat-import Python modules can be relocated together without rewriting imports]]
- [[Strip all comments and docstrings from Python safely with tokenize plus AST]]

%% ai-graph-end %%