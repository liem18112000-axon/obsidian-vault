---
title: "Add cross-stage provenance to a shared graph via update_index without new model methods"
created: 2026-08-28
type: lesson
status: seedling
source: "session 2026-08-28, test_plan_definition M3"
tags: [test-agent, memory-bank, provenance, graph, design-decision]
---

# Add cross-stage provenance to a shared graph via update_index without new model methods

The implement stage records cross-stage provenance (test-plan and test-scenario nodes, with edges scenario -> insight/note) into the SAME knowledge-index graph that gather/refine populate. It does this without adding `add_plan`/`add_scenario` methods to `knowledge_gathering.models.Graph`: the shared `MemoryBank.update_index(mutate)` runs a callback that writes directly into the graph's public `nodes` / `edges` dicts (the exact shape `Graph.add_insight` uses internally).

```python
bank.update_index(lambda g: _add_provenance(g, plan, scenarios))

def _add_provenance(graph, plan, scenarios):
    graph.nodes[plan.id] = {"id": plan.id, "type": TEST_PLAN, "title": ...}
    for ref in plan.source_refs:
        graph.edges[f"{plan.id}->{ref}"] = {"source_id": plan.id, "target": ref, ...}
    for sc in scenarios:
        graph.nodes[sc.id] = {"id": sc.id, "type": TEST_SCENARIO, "title": sc.title}
        for ref in sc.source_refs:
            graph.edges[f"{sc.id}->{ref}"] = {...}
```

**Why:** keeps the reusable `Graph` model free of stage-specific vocabulary (a new stage would otherwise keep bolting methods onto it), while still landing in one queryable graph. `update_index` already does the compare-and-set retry + index-markdown re-render, so the mutate callback is the right seam. Trade-off: the node/edge dict shape (`id/type/title`; `source_id/target/type/origin/in_scope`) is now an informal contract two packages depend on — keep it stable.

## Related

- [[Testing Agent builds each pipeline stage as a package mirroring the knowledge_gathering skeleton]]
