---
title: "test-agent two A2A agents share a skeleton but diverge in domain engines"
created: 2026-08-31
type: observation
status: seedling
source: "session 2026-08-31"
tags: [test-agent, a2a, package-structure, architecture]
---

# test-agent two A2A agents share a skeleton but diverge in domain engines

The test-agent monorepo has two A2A agents — **knowledge_gathering** (KGA, a read-only Atlassian crawler) and **test_plan_definition** (TPD, a test-plan author). They deliberately share an **identical skeleton** but **diverge in their domain engines**.

**Identical skeleton (the A2A-agent shape):** both have `__init__`, `constants`, `monitoring`, `server`, `bridge/{__init__,__main__,mcp_server}`, `executor/{__init__,base,common}`, and `models/`. This is the framework/contract layer every A2A agent needs — aligning it aids navigation.

**Divergent domain engines (by job):**
- KGA: `loop/` (seed -> fetch/ -> crawl) — one small crawl engine; executor handlers `gather`/`refine`.
- TPD: `define/` + `implement/` + `llm/` + `memory/` + `render/` (gherkin); executor handlers `define`/`implement`. More packages because it does more: interrogate -> plan -> generate testdata/scenarios/steps -> render.

**Tell-tale asymmetry that proves the principle:** KGA has *no local* `llm/` because its LLM work is generic (distill) and hoisted into `common/llm`; TPD keeps a local `llm/` because its prompts are domain-specific. Generic work migrated up, specific work stayed local.

Related invariant: `common` never imports an agent, and the two agents never import each other. This is the worked example behind [[Service symmetry belongs at the contract layer not the domain layer]].

## Related

- [[Service symmetry belongs at the contract layer not the domain layer]]
