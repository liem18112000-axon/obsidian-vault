---
title: "Slide 32: Common Pitfalls & How to Avoid Them"
slide_number: 32
tags: [lecture/slide, langgraph, agentic-programming]
source: https://github.com/trieu/ai-trip-planner/blob/main/docs/AGENTIC_PROGRAMMING_LECTURE.md
---

# Slide 32: Common Pitfalls & How to Avoid Them

### Pitfall 1: State Mutations

```python
# ❌ WRONG
def node(state):
    state["data"].append("x")  # Mutates shared state
    return state

# ✅ RIGHT
def node(state):
    new_data = state.get("data", []) + ["x"]
    return {"data": new_data}
```

### Pitfall 2: Forgetting to Return Dict Updates

```python
# ❌ WRONG - returns old values
def node(state):
    result = expensive_computation()
    # OOPS! Forgot to return

# ✅ RIGHT
def node(state):
    result = expensive_computation()
    return {"output": result}
```

### Pitfall 3: Unhandled Exceptions

```python
# ❌ WRONG - crashes entire workflow
def node(state):
    result = external_api()  # Could fail!
    return {"data": result}

# ✅ RIGHT - graceful fallback
def node(state):
    try:
        result = external_api()
    except Exception as e:
        logger.error(f"API failed: {e}")
        result = "Fallback: using cached data"
    
    return {"data": result}
```

### Pitfall 4: Blocking Calls in Async Nodes

```python
# ❌ WRONG - blocks event loop
async def node(state):
    result = requests.get(url)  # Blocking!
    return {"data": result}

# ✅ RIGHT - async HTTP
async def node(state):
    async with aiohttp.ClientSession() as session:
        result = await session.get(url)  # Non-blocking
    return {"data": result}
```

---

← [[Slide 31 - Scaling & Performance Optimization|Previous]] · [[Index|🏠 Index]] · [[Slide 33 - Debugging Techniques|Next]] →
