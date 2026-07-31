---
ai_hash: e3aa54a4d4ffe915
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-03
entities: []
source: session 2026-07-03 appsflyer-data-connector
status: seedling
tags:
- python
- refactoring
- architecture
title: Extracting a shared utils package - classify by whether code knows source semantics
type: lesson
---

# Extracting a shared utils package - classify by whether code knows source semantics

When pulling "non-business utilities" out of a connector/domain package into a shared `common/utils`, classify each candidate by one test: **does the code know any source/domain semantics?** Applied in appsflyer-data-connector:

- **Moves to utils** — code that would read identically in any connector: date-arg parsing/expansion, CSV text ⇄ rows, naive-timestamp→UTC-ISO conversion, seeded-RNG value primitives, the shared `logging.basicConfig` line (only extract this one because it repeated in 4 CLIs — never for a single site).
- **Moves, but NOT to utils** — shared plumbing that belongs to an existing owner module: sink-from-env builders went to `common/sink/factory.py` (the module that already owns sink construction), not utils. Bonus: this killed `from .cli import _private` cross-module imports, which are a smell that marks a helper living in the wrong home.
- **Stays** — anything encoding domain conventions even if it looks generic: header alias maps, partition-naming schemes (`{app}__{report}__{day}`), domain id formats (`_af_id`), event registries.

Name utils modules to dodge stdlib confusion (`csvio`, `timeutil`, `rand`, `logsetup` — not `csv.py`/`time.py`; absolute imports make shadowing harmless in py3, but readers stumble).

Mechanics follow [[Convert a Python module to a package without breaking importers via re-exporting __init__]] (relocation variant: rewrite import sites, no old-path shims).

## Related

- [[Convert a Python module to a package without breaking importers via re-exporting __init__]]

**Follow-up (resolving the flagged wart):** when a moved-to-common helper still carries a domain literal (e.g. `source="appsflyer"` inside `common/sink/factory.build_sink`), de-domain-ize it by promoting the literal to a keyword parameter whose default is the *generic* behavior (`source: str | None = None` → fall back to each event's own source), and have the existing domain callers pass the old literal explicitly. Behavior stays byte-identical for current callers while the common module stops knowing about any one connector.

**Sed-rename gotcha:** an underscore-prefixed name is a substring of any snake_case name ending in the same word, so `s/_partition(/partition_key(/g` also mangles `write_partition(` → `writepartition_key(`. `\b` is unreliable here (`_` is a word char). Anchor on an explicit non-identifier char instead: `s/\([^a-zA-Z0-9_]\)_partition(/\1partition_key(/g` plus a line-start variant — then grep for damaged identifiers (tests caught this one via `AttributeError`).

%% ai-graph-start %%

**Related notes:**
- [[Convert a Python module to a package without breaking importers via re-exporting __init__]]
- [[AppsFlyer package layout package-per-concern with no loose modules]]
- [[Monkeypatched module attributes are a hidden breakage risk when a module becomes a package]]
- [[Extract shared use-case code into a sibling shared package, not a peer use case]]
- [[Grep-audit env vars against code before pruning .env files]]

%% ai-graph-end %%