---
ai_hash: 4eee0b587bd5d63f
ai_model: google/gemini-2.5-flash
ai_updated: '2026-05-30'
entities:
- Slide 9
- Node Functions
- Basic Node Structure
- Node function
- Current state
- Dict update
- Sync
- Async
- Return Dict
- Full State
- Slide 8
- Defining Edges (Control Flow)
- Index
- Slide 10
- The Profile Node (Personalization)
slide_number: 9
source: https://github.com/trieu/ai-trip-planner/blob/main/docs/AGENTIC_PROGRAMMING_LECTURE.md
tags:
- lecture/slide
- langgraph
- agentic-programming
title: 'Slide 9: Writing Node Functions'
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

%% ai-graph-start %%

**Related notes:**
- [[Slide 6 - State Definition in LangGraph]]
- [[Slide 10 - The Profile Node (Personalization)]]
- [[Slide 27 - Tool Calling in Nodes]]
- [[Slide 8 - Defining Edges (Control Flow)]]
- [[Slide 21 - Testing Agentic Systems]]

**Relations:**
- Slide 9 — *discusses* — Node Functions
- Node Functions — *have* — Basic Node Structure
- Node function — *takes* — Current state
- Node function — *returns* — Dict update
- Node function — *can be* — Sync
- Node function — *can be* — Async
- Dict update — *is not* — Full State
- Return Dict — *is* — Correct
- Full State — *is* — Wrong
- Slide 9 — *follows* — Slide 8
- Slide 8 — *is titled* — Defining Edges (Control Flow)
- Slide 9 — *precedes* — Slide 10
- Slide 10 — *is titled* — The Profile Node (Personalization)
- Slide 9 — *links to* — Index

%% ai-graph-end %%