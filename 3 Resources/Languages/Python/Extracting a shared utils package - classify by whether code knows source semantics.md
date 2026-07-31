---
title: "Extracting a shared utils package - classify by whether code knows source semantics"
created: 2026-07-03
type: lesson
status: seedling
source: "session 2026-07-03 appsflyer-data-connector"
tags: [python, refactoring, architecture]
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

**Sed-rename gotcha (bitten in practice):** renaming `_partition(` → `partition_key(` with plain `s/_partition(/partition_key(/g` also rewrote the *suffix* of `write_partition(` → `writepartition_key(`. Underscore-prefixed names are substrings of snake_case names ending in the same word. Anchor renames with a word-ish left boundary — GNU sed `s/\b_partition(/…/` does NOT work (\b matches between `e` and `_`? no — _ is a word char, so \b fails to fire inside write_partition, which would actually be safe) — the reliable guard is `s/\([^a-zA-Z0-9_]\)_partition(/\1partition_key(/g` plus a line-start variant, or just grep the result for damaged identifiers immediately (tests caught it here via AttributeError).
