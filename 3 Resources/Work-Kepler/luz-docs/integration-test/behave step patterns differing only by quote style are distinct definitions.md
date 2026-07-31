---
ai_hash: 7307d5111580bcd1
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-16
entities: []
source: session 2026-06-16
status: evergreen
tags:
- behave
- integration-test
- gotcha
- step-definitions
title: behave step patterns differing only by quote style are distinct definitions
type: gotcha
---

# behave step patterns differing only by quote style are distinct definitions

In behave, step patterns that differ only by **quote style** — single `'x'` vs double `"x"` — are **distinct step definitions** and can live in different step files with different behavior. behave matches the literal step text including quotes, so `@when('"'"{action}"'"' security class '"'"{x}"'"' for the root folder")` and `@when('"{op}" security class "{values}" for the root folder')` are two separate steps.

Gotcha that bit luz-docs IT: there were two near-identical "... security class ... for the root folder" steps — a single-quote one in `search_document_steps.py` that did NOT resolve `$SCn`, and a double-quote one in `update_security_classes_steps.py` that DID (via `_resolve_sc_codes`). A feature written with the single-quote phrasing silently bypassed resolution and sent the literal placeholder. A `behave --dry-run` reports 0 undefined/0 ambiguous because the patterns are genuinely different, so it does not flag the divergence.

Takeaway: when two steps read the same in prose, normalize quote style or consolidate them; otherwise a feature can bind to the wrong (stale) implementation without any warning.

Related: [[luz-docs IT $SCn authorization failures were deterministic resolution regressions, not membership]]

## Related

- [[luz-docs IT $SCn authorization failures were deterministic resolution regressions, not membership]]

%% ai-graph-start %%

**Related notes:**
- [[luz-docs IT $SCn authorization failures were deterministic resolution regressions, not membership]]
- [[luz-docs IT before_scenario reads but does not substitute $SCn into step text]]
- [[luz-docs IT $SCn security-class placeholders resolve against the token claim]]
- [[luz-docs IT skips security-class scenarios when the tenant token lacks enough codes]]
- [[Isolate the same scenarios on both branches to separate regression from flakiness]]

%% ai-graph-end %%