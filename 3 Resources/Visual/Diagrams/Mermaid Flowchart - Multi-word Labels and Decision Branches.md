---
title: "Mermaid Flowchart - Multi-word Labels and Decision Branches"
created: 2026-07-08
type: howto
status: seedling
tags: [mermaid, diagram, flowchart, syntax]
---

# Mermaid Flowchart - Multi-word Labels and Decision Branches

Mermaid flowchart nodes support multi-word labels natively — no escaping needed. Use square brackets for rectangular nodes and curly braces for diamond decision nodes. Edge labels go between pipes.

Key syntax reference:
- [Label text] — rectangle node (process step)
- {Decision text} — diamond node (branch point)
- -->|Edge label| — labeled directed edge
- flowchart TD — top-down layout; use LR for left-to-right

Decisions loop back by reusing a node ID (e.g. D --> B sends the unauthenticated path back to a previous check node).
