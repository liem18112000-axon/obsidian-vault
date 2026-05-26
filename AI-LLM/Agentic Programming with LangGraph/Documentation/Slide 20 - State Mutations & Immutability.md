---
title: "Slide 20: State Mutations & Immutability"
slide_number: 20
tags: [lecture/slide, langgraph, agentic-programming]
source: https://github.com/trieu/ai-trip-planner/blob/main/docs/AGENTIC_PROGRAMMING_LECTURE.md
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
