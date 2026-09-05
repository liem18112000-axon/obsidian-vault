---
title: "pytest collects domain classes named Test* — narrow python_classes"
created: 2026-08-28
type: lesson
status: seedling
source: "session 2026-08-28, test_plan_definition M0 scaffold"
tags: [pytest, python, gotcha, testing]
---

# pytest collects domain classes named Test* — narrow python_classes

pytest's default class-collection heuristic treats any class whose name starts with `Test` as a test class. If your **domain model** is named `Test*` (e.g. `TestPlan`, `TestData`, `TestScenario`, `TestStep`), pytest tries to collect it and emits `PytestCollectionWarning: cannot collect test class 'TestPlan' because it has a __init__ constructor`. It is only a warning (the class is skipped, no phantom tests run), but it is recurring noise and would become fragile if such a class ever lacked an `__init__`.

**Fix (project-wide, when the suite uses only function-style tests):** narrow the collection pattern in `pyproject.toml` so classes need a distinctive suffix, not the `Test` prefix:
```
[tool.pytest.ini_options]
python_classes = ["*Tests"]
```
Verify first that no existing test relies on a `Test`-prefixed class being collected (`grep -rnE "^class Test" tests/`). Alternatives: set `__test__ = False` on each model (pollutes domain code), or just accept the warning.

Applies whenever a testing domain legitimately owns `Test*` type names — common in QA/test-tooling code.
