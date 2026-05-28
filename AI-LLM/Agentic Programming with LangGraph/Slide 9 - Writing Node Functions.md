---
title: "Slide 9: Writing Node Functions"
slide_number: 9
tags: [lecture/slide, langgraph, agentic-programming]
source: https://github.com/trieu/ai-trip-planner/blob/main/docs/AGENTIC_PROGRAMMING_LECTURE.md
---

# Slide 9: Writing Node Functions

### Basic Node Structure

A node function:
1. Takes the current state
2. Returns a dict update (not replacement!)
3. Can be sync or async

```python
def my_node(state: TripState) -> Dict[str, Any]:
    # Extract what you need
    destination = state["trip_request"]["destination"]
    profile = state["user_profile"]
    
    # Do work (LLM calls, tool calls, etc.)
    result = some_computation(destination, profile)
    
    # Return only the fields you update
    return {
        "research": result,
        "tool_calls": [{"tool": "get_destination_info", "args": {...}}]
    }
```

### ⚠️ Critical: Return Dict, Not Full State

```python
# ❌ WRONG - returns entire state
return state  # This overwrites everything!

# ✅ CORRECT - returns only updated fields
return {"research": result}

# ✅ CORRECT - updates multiple fields
return {
    "research": result,
    "messages": [msg],
    "tool_calls": [...]
}
```

---

← [[Slide 8 - Defining Edges (Control Flow)|Previous]] · [[Index|🏠 Index]] · [[Slide 10 - The Profile Node (Personalization)|Next]] →
