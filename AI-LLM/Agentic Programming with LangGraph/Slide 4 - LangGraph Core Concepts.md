---
title: "Slide 4: LangGraph Core Concepts"
slide_number: 4
tags: [lecture/slide, langgraph, agentic-programming]
source: https://github.com/trieu/ai-trip-planner/blob/main/docs/AGENTIC_PROGRAMMING_LECTURE.md
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
