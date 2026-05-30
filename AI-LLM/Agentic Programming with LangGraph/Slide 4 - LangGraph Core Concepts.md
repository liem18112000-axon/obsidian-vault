---
ai_hash: 1b31b5222d243a2a
ai_model: google/gemini-2.5-flash
ai_updated: '2026-05-30'
entities:
- LangGraph
- Stateful, multi-actor applications
- Large language models
- StateGraph
- Nodes
- Edges
- Workflow structure
- Functions
- Agents
- Tools
- Logic
- Control flow
- Restaurant kitchen
- OrderNode
- CookNode
- IngredientNode
- QCNode
- ServeNode
- Slide 4 - LangGraph Core Concepts
- Slide 3
- AI Trip Planner - Your Real-World Blueprint
- Index
- Slide 5
- The Trip Planner Architecture
slide_number: 4
source: https://github.com/trieu/ai-trip-planner/blob/main/docs/AGENTIC_PROGRAMMING_LECTURE.md
tags:
- lecture/slide
- langgraph
- agentic-programming
title: 'Slide 4: LangGraph Core Concepts'
---

# Slide 4: LangGraph Core Concepts

### What is LangGraph?

A library for building **stateful, multi-actor applications** with large language models.

### Three Key Components

1. **StateGraph**: Defines your workflow structure
2. **Nodes**: Functions that process state (agents, tools, logic)
3. **Edges**: Connections between nodes (control flow)

### Real-World Analogy

Think of a workflow like a restaurant kitchen:

```
OrderNode (take order) 
    ↓
CookNode (prepare dish) ← IngredientNode (fetch ingredients)
    ↓
QCNode (quality check)
    ↓
ServeNode (serve customer)
```

Each node is autonomous; the graph orchestrates flow.

---

← [[Slide 3 - The AI Trip Planner - Your Real-World Blueprint|Previous]] · [[Index|🏠 Index]] · [[Slide 5 - The Trip Planner Architecture|Next]] →

%% ai-graph-start %%

**Related notes:**
- [[Slide 7 - Building the Graph]]
- [[Slide 1 - Course Overview]]
- [[Appendix B - Quick Reference]]
- [[Index]]
- [[Slide 40 - Recap & Your Turn]]

**Relations:**
- LangGraph — *is a library for* — Stateful, multi-actor applications
- Stateful, multi-actor applications — *use* — Large language models
- LangGraph — *has component* — StateGraph
- LangGraph — *has component* — Nodes
- LangGraph — *has component* — Edges
- StateGraph — *defines* — Workflow structure
- Nodes — *are* — Functions
- Nodes — *process* — state
- Nodes — *include* — Agents
- Nodes — *include* — Tools
- Nodes — *include* — Logic
- Edges — *are* — Connections
- Edges — *define* — Control flow
- Workflow — *analogy* — Restaurant kitchen
- OrderNode — *is a type of* — Nodes
- CookNode — *is a type of* — Nodes
- IngredientNode — *is a type of* — Nodes
- QCNode — *is a type of* — Nodes
- ServeNode — *is a type of* — Nodes
- OrderNode — *precedes* — CookNode
- IngredientNode — *provides to* — CookNode
- CookNode — *precedes* — QCNode
- QCNode — *precedes* — ServeNode
- Slide 3 — *is previous to* — Slide 4 - LangGraph Core Concepts
- Slide 3 — *has title* — AI Trip Planner - Your Real-World Blueprint
- Slide 4 - LangGraph Core Concepts — *links to* — Index
- Slide 5 — *is next to* — Slide 4 - LangGraph Core Concepts
- Slide 5 — *has title* — The Trip Planner Architecture

%% ai-graph-end %%