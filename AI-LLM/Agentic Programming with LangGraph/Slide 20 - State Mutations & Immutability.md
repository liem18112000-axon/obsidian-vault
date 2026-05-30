---
ai_hash: fe65ff68102cfa03
ai_model: google/gemini-2.5-flash
ai_updated: '2026-05-30'
entities:
- Slide 20
- State Mutations
- Immutability
- Common Mistake
- bad_node
- TripState
- synchronization bugs
- good_node
- Annotated Lists
- operator.add
- tool_calls
- Slide 19 - Conditional Branching (Advanced)
- Index
- Slide 21 - Testing Agentic Systems
slide_number: 20
source: https://github.com/trieu/ai-trip-planner/blob/main/docs/AGENTIC_PROGRAMMING_LECTURE.md
tags:
- lecture/slide
- langgraph
- agentic-programming
title: 'Slide 20: State Mutations & Immutability'
---

# Slide 20: State Mutations & Immutability

### ⚠️ Common Mistake: State Mutation

```python
# ❌ WRONG - modifies state in place
def bad_node(state: TripState) -> Dict[str, Any]:
    state["research"].append("new data")  # Mutates!
    return state  # Causes synchronization bugs!

# ✅ CORRECT - returns new data
def good_node(state: TripState) -> Dict[str, Any]:
    existing = state.get("research", "")
    new_research = existing + "\nnew data"
    return {"research": new_research}
```

### With Annotated Lists (operator.add)

```python
# In state definition
tool_calls: Annotated[List[Dict[str, Any]], operator.add]

# In node, DON'T DO THIS:
state["tool_calls"].append(new_call)  # ❌ Mutation

# DO THIS:
return {"tool_calls": [new_call]}  # ✅ operator.add handles concatenation
```

---

← [[Slide 19 - Conditional Branching (Advanced)|Previous]] · [[Index|🏠 Index]] · [[Slide 21 - Testing Agentic Systems|Next]] →

%% ai-graph-start %%

**Related notes:**
- [[Slide 32 - Common Pitfalls & How to Avoid Them]]
- [[Slide 6 - State Definition in LangGraph]]
- [[Slide 21 - Testing Agentic Systems]]
- [[Slide 9 - Writing Node Functions]]
- [[Slide 19 - Conditional Branching (Advanced)]]

**Relations:**
- Slide 20 — *discusses* — State Mutations
- Slide 20 — *discusses* — Immutability
- State Mutations — *are a* — Common Mistake
- bad_node — *demonstrates* — State Mutations
- bad_node — *modifies* — TripState
- State Mutations — *cause* — synchronization bugs
- good_node — *demonstrates* — Immutability
- Annotated Lists — *use* — operator.add
- operator.add — *handles* — concatenation
- tool_calls — *is an example of* — Annotated Lists
- Slide 20 — *follows* — Slide 19 - Conditional Branching (Advanced)
- Slide 20 — *precedes* — Slide 21 - Testing Agentic Systems
- Slide 20 — *links to* — Index

%% ai-graph-end %%