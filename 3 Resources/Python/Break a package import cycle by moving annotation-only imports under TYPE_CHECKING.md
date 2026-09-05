---
title: "Break a package import cycle by moving annotation-only imports under TYPE_CHECKING"
created: 2026-08-28
type: lesson
status: seedling
source: "session 2026-08-28 (KGA llm/ refactor)"
tags: [python, imports, circular-import, type-checking, gotcha]
---

# Break a package import cycle by moving annotation-only imports under TYPE_CHECKING

When you split a module and the new home imports a class from another subpackage **only for type annotations**, a runtime `from pkg.sub.mod import X` can create an import cycle — because importing a submodule first runs the parent package `__init__.py`, and if that `__init__` eagerly imports the very module you just moved code out of, Python hits a partially-initialized module and raises `ImportError: cannot import name ... (most likely due to a circular import)`.

Fix: with `from __future__ import annotations` (PEP 563) all annotations are stored as strings and never evaluated at import time, so the type only needs to exist for static checkers. Move the offending import under a `TYPE_CHECKING` guard:
```python
from __future__ import annotations
from typing import TYPE_CHECKING
if TYPE_CHECKING:
    from pkg.sub.mod import X   # annotation-only; no runtime import, no cycle
```
This severs the runtime edge (llm -> refine) while keeping full type hints. Only works when X is used purely as an annotation — if it is referenced at runtime (constructed, isinstance-checked, subclassed), keep the real import and break the cycle another way (lazy import inside the function, or restructure the package __init__ to not eagerly import the leaf module).

Surfaced centralizing an `llm/` package out of `refine/`: `llm.prompts` needed `refine.pack.Pack` only for annotations, but importing it pulled in `refine/__init__` -> `refine.loop` -> `refine.questions` -> back into the half-built `llm.questions`.
