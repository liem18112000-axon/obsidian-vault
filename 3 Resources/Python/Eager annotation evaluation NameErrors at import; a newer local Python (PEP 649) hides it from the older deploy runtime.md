---
title: "Eager annotation evaluation NameErrors at import; a newer local Python (PEP 649) hides it from the older deploy runtime"
created: 2026-08-20
type: gotcha
status: seedling
source: "leo-customer360 identity.py main-sync, 2026-08"
tags: [python, annotations, pep649, import, version-skew, gotcha]
---

# Eager annotation evaluation NameErrors at import; a newer local Python (PEP 649) hides it from the older deploy runtime

Python evaluates function annotations EAGERLY at def-time (module import) unless `from __future__ import annotations` is set — so an annotation that names an unimported symbol, e.g. `def f(...) -> Optional[datetime]:` when `datetime` was never imported, raises `NameError: name ... is not defined` at IMPORT, taking down the whole module (and the app that imports it). py_compile does NOT catch it (compile != evaluate); only an actual import does.

**Version-skew trap:** Python **3.14** ships PEP 649 (deferred/lazy annotation evaluation), so the SAME code imports fine on a 3.14 dev machine but crashes on a 3.11 container. A local repro that prints "OK" on 3.14 is not proof — reproduce in the deploy runtime (`docker exec <ctr> python -c ...`) or match the version. Real incident: a synced/merged file kept an orphaned helper with `-> Optional[datetime]` and dropped the `from datetime import ...`; dev (3.14) was green, the 3.11 image would have crash-looped on redeploy.

Lessons: (1) when a merge removes an import, grep the file for every use of the removed name — including inside annotations and dead/orphaned functions. (2) Validate imports on the TARGET Python version, not just locally. (3) Dead code with a bad annotation is still fatal at import.

Related: [[A client CORS/unreachable-API error can mask a backend 500 — read the server log]]
