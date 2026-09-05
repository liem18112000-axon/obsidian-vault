---
title: "Python __init_subclass__ auto-registering strategy registry"
created: 2026-08-30
type: howto
status: seedling
source: "session 2026-08-30 fetch refactor"
tags: [python, design-patterns, strategy, solid, open-closed]
---

# Python __init_subclass__ auto-registering strategy registry

To replace an `if kind == "a": ... elif kind == "b": ...` type-switch with polymorphism (SOLID: Open/Closed + Single Responsibility), give an abstract base a class-level registry and let each subclass self-register via `__init_subclass__`. The dispatcher then looks the handler up by key — adding a case is a new subclass file, never an edit to the dispatcher.

```python
from abc import ABC, abstractmethod
from typing import ClassVar

class NodeFetcher(ABC):
    kind: ClassVar[str] = ""                       # discriminator each subclass sets
    registry: ClassVar[dict[str, "NodeFetcher"]] = {}

    def __init_subclass__(cls, **kw):
        super().__init_subclass__(**kw)
        if cls.kind:
            NodeFetcher.registry[cls.kind] = cls()  # store an instance (stateless strategy)

    @abstractmethod
    async def fetch(self, ...): ...

# dispatcher — no if/elif, closed for modification:
def fetch_node(nid, ...):
    kind, _, ident = nid.partition(":")
    fetcher = NodeFetcher.registry.get(kind)
    if fetcher is None: raise ValueError(f"unhandled: {nid}")
    return fetcher.fetch(...)
```

Gotchas: (1) subclasses only register when their module is IMPORTED — so the package `__init__` must import each subclass module (mark `# noqa: F401`), otherwise the registry is empty. (2) Mutable class attrs (`registry = {}`) trip ruff RUF012 — annotate with `typing.ClassVar`. (3) This is really the Strategy pattern selected by a discriminator (often mislabelled "state pattern"); State is for behavior that changes with an objects own mutable state. Package layout that pairs well: one subclass per file + a base.py + an `__init__` that imports them and exposes the dispatcher. Related: [[test-agent-common-shared-engine]].
