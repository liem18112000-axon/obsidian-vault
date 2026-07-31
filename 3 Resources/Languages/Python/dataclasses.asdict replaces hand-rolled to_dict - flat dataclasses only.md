---
ai_hash: 473e6eee5228e4e7
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-03
entities: []
status: seedling
tags:
- python
- dataclasses
- simplification
title: dataclasses.asdict replaces hand-rolled to_dict - flat dataclasses only
type: lesson
---

# dataclasses.asdict replaces hand-rolled to_dict - flat dataclasses only

A hand-written `to_dict` on a dataclass that just mirrors its fields is dead code — `dataclasses.asdict(self)` produces the same dict, preserves field order, and works with `@dataclass(slots=True)`. Replaced a 5-line literal in appsflyer-data-connector's `DayResult` with `return asdict(self)`.

Caveat that keeps this from being a blind rewrite: `asdict` is **recursive and deep-copying** — nested dataclasses/lists/dicts are converted and copied, whereas a hand-rolled version usually shares references and keeps nested objects as-is. Drop-in only for flat, scalar-field dataclasses; if callers rely on identity of nested values, use `{f.name: getattr(self, f.name) for f in fields(self)}` instead.

%% ai-graph-start %%

**Related notes:**
- _(none above threshold)_

%% ai-graph-end %%