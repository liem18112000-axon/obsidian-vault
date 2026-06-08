---
title: "Comparing _folderSecurityClassCodes in tests needs a multiset-of-sets, not a flat set"
created: 2026-06-08
type: lesson
status: seedling
source: "session 2026-06-08 LUZ-154157"
tags: [luz-docs, materialize, testing, python, gotcha]
---

# Comparing _folderSecurityClassCodes in tests needs a multiset-of-sets, not a flat set

Because the Luz materialized field `_folderSecurityClassCodes` is a **list of lists** (one inner list per folder, duplicates kept, outer order not guaranteed), asserting it in tests with a flat `set()` is wrong — `set()` on nested lists raises (unhashable), and even after flattening it would collapse duplicate folders and lose the per-folder structure.

Compare it as a **multiset of sets**: normalize both sides by sorting each inner list (deduped) and sorting the outer list while **keeping duplicate inner lists**. This is order-insensitive on the outer list but still distinguishes `[["X"],["X"]]` from `[["X"]]`.

Helper used in `features/steps/delete_folder_materialized_steps.py`:

```python
def _norm_nested(value):
    return sorted(tuple(sorted(set(inner or []))) for inner in (value or []))
```

`tuple(...)` makes each inner hashable/comparable; sorting the outer gives order-insensitive equality; using a list (not a set) for the outer keeps duplicates.

## Related
[[Luz _folderSecurityClassCodes is a list-of-lists, one inner list per folder]]

## Related

- [[Luz _folderSecurityClassCodes is a list-of-lists]]
- [[one inner list per folder]]
