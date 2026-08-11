---
ai_hash: cc8acaf757c2338d
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-08
entities: []
source: session 2026-07-08
status: seedling
tags:
- mermaid
- diagram
- diagrams
- flowchart
- syntax
title: Mermaid Flowchart - Multi-word Labels and Decision Branches
type: howto
updated: 2026-07-31
---

# Mermaid Flowchart - Multi-word Labels and Decision Branches

Mermaid flowchart node labels support multi-word text natively — spaces inside the bracket delimiters need no escaping or quoting. Branch logic is expressed purely by edge syntax, so a whole decision tree needs no extra directives.

## Syntax

| Element | Syntax | Use |
|---|---|---|
| Rectangle | `[Multi word label]` | Process / step |
| Diamond | `{Is user authenticated?}` | Decision / branch |
| Rounded | `(Label)` | Start / end |
| Labeled edge | `A -->\|Yes\| B` | Branch condition (label between pipes) |
| Direction | `flowchart TD` | Top-down (default); also `LR`, `BT`, `RL` |

## Branching rules

- Multiple outgoing edges from one node = branches.
- Multiple incoming edges to one node = converging paths; valid with no special syntax.
- Back-edges (reusing an earlier node ID, e.g. `D --> B`) are valid retries/loops; Mermaid renders them as curved arrows.

```mermaid
flowchart TD
    A[Receive incoming request] --> B{Is user authenticated?}
    B -->|Yes| C{Has sufficient permissions?}
    B -->|No| D[Redirect to login page]
    C -->|Yes| E[Process the request]
    C -->|No| F[Show access denied error]
    E --> G[Return successful response]
    D --> B
    F --> G
```

%% ai-graph-start %%

**Related notes:**
- [[LLM-generated mermaid sequenceDiagrams die on semicolons and reserved-word aliases]]
- [[Mermaid text clipping causes useMaxWidth shrink, narrow wrappingWidth, and web-font race]]
- [[Mermaid's global htmlLabels option overrides the deprecated per-diagram-type ones]]

%% ai-graph-end %%