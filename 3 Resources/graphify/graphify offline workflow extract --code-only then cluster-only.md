---
title: "graphify offline workflow: extract --code-only then cluster-only"
created: 2026-08-24
type: howto
status: seedling
source: "session 2026-08-24 ecomart-java analysis"
tags: [graphify, code-graph, tree-sitter, static-analysis, workflow]
---

# graphify offline workflow: extract --code-only then cluster-only

To build a code knowledge graph **fully locally** (tree-sitter, no LLM, nothing leaves the machine), run two steps — not one:

```bash
graphify extract .  --code-only      # AST-only graph.json (respects .gitignore/.graphifyignore)
graphify cluster-only . --no-label   # THEN generates GRAPH_REPORT.md + graph.html, offline
```

Key gotcha: `extract --code-only` writes `graph.json` but **not** the report or HTML — you must follow it with `cluster-only` (use `--no-label` to skip the LLM community-naming and stay 100% offline). Output lands in `<target>/graphify-out/`.

Investigate offline with: `graphify god-nodes --top N` (hubs), `query "question"`, `path A B` (shortest link), `affected X` (reverse impact), `explain X`.

**Scope limit:** graphify maps **structure only** — calls, imports, inheritance, references (`EXTRACTED` = AST fact, `INFERRED` = guess). It never captures business logic, domain rules, or invariants. Use it to **locate** code fast, then read the source + `*Test.java` (tests are the concrete spec) to understand the *why*.

PyPI package is `graphifyy` (double-y); CLI is `graphify` (single-y).
