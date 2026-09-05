---
title: "Test an LLM-vs-heuristic seam offline by monkeypatching complete() per module"
created: 2026-08-28
type: lesson
status: seedling
source: "session 2026-08-28, test_plan_definition M6"
tags: [test-agent, testing, llm, vertex, pytest]
---

# Test an LLM-vs-heuristic seam offline by monkeypatching complete() per module

Each generator has a `make_*()` factory that returns a Claude-on-Vertex path when `vertex_config()` (VERTEX_PROJECT/LOCATION/MODEL) is set, else a deterministic heuristic. To test both paths with NO credentials and NO network:

- **Heuristic default:** `monkeypatch.delenv("VERTEX_PROJECT")` → the factory returns the heuristic; assert its deterministic output.
- **Claude path:** `monkeypatch.setenv` the three VERTEX_* vars, THEN `monkeypatch.setattr("test_plan_definition.llm.questions.complete", lambda *a, **k: CANNED_JSON)`. The factory picks the Claude branch, and the canned JSON exercises the real prompt-build + parse + dataclass-construction without hitting Vertex.

Key detail: patch `complete` in the MODULE THAT IMPORTED IT (`...llm.questions.complete`, `...llm.plan.complete`, `...llm.scenarios.complete`) — because it was pulled in via `from knowledge_gathering.llm.vertex import complete`, so the name to patch is the local binding, not the source module. Also test the fallback: feed non-JSON and assert the caller drops back to the heuristic (the parser returns None on a non-array). This mirrors how knowledge_gathering tests its refine LLM seam.

## Related

- [[Testing Agent builds each pipeline stage as a package mirroring the knowledge_gathering skeleton]]
