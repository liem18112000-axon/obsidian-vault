---
title: "Graphify for code reviews without source access"
created: 2026-07-14
type: technique
status: seedling
tags: [code-review, graphify, architecture, luz-docs]
---

# Graphify for code reviews without source access

When source files are not locally available, use Graphify graph queries to extract and analyze code structure directly. The graph is built during compilation and contains the full AST—all classes, methods, fields, imports, and edges (who calls whom, who imports what).

**Query patterns:**
- `explain CLASSNAME` — returns the node, its degree (edge count), and all 1-hop connections (incoming/outgoing).
- `query "what does X do"` — semantic search over the graph, returns nodes matching the query.
- `path "A" "B"` — shortest dependency path between two nodes.

**Why this works for code review:** You get architecture coupling, component count, method signatures, test coverage (visible as test class imports), and dependency direction—without needing source. Excellent for feature-layer analysis when the codebase is large and staging/source is ephemeral (cleaned up after build).

**Example:** Analyzed materialize feature (4 classes, 49+32+17+16 edges) by querying Graphify instead of reading .java files, identified cascade complexity, high facade coupling, and recommended index verification.

Works best for: architecture reviews, coupling assessment, layer integrity checks, identifying integration risks.

## Related

- [[Code review patterns]]
- [[Materialize feature architecture]]
