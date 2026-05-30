---
ai_hash: 7870a4330f3fc85e
ai_model: google/gemini-2.5-flash
ai_updated: '2026-05-30'
entities:
- Slide 32
- Common Pitfalls
- State Mutations
- Forgetting to Return Dict Updates
- Unhandled Exceptions
- Blocking Calls in Async Nodes
- '`node` function'
- '`state`'
- '`requests.get`'
- '`aiohttp.ClientSession`'
- '`event loop`'
- '`try-except` block'
- '`logger.error`'
- Slide 31
- Index
- Slide 33
- shared state
- old values
- workflow crashes
- graceful fallback
- Non-blocking
- Blocking Call
- async HTTP
slide_number: 32
source: https://github.com/trieu/ai-trip-planner/blob/main/docs/AGENTIC_PROGRAMMING_LECTURE.md
tags:
- lecture/slide
- langgraph
- agentic-programming
title: 'Slide 32: Common Pitfalls & How to Avoid Them'
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

%% ai-graph-start %%

**Related notes:**
- [[Slide 20 - State Mutations & Immutability]]
- [[Slide 9 - Writing Node Functions]]
- [[Slide 33 - Debugging Techniques]]
- [[Slide 27 - Tool Calling in Nodes]]
- [[Slide 12 - The Weather Node (Real-Time Data)]]

**Relations:**
- Slide 32 — *covers* — Common Pitfalls
- Common Pitfalls — *include* — State Mutations
- Common Pitfalls — *include* — Forgetting to Return Dict Updates
- Common Pitfalls — *include* — Unhandled Exceptions
- Common Pitfalls — *include* — Blocking Calls in Async Nodes
- State Mutations — *is a type of* — Pitfall
- Forgetting to Return Dict Updates — *is a type of* — Pitfall
- Unhandled Exceptions — *is a type of* — Pitfall
- Blocking Calls in Async Nodes — *is a type of* — Pitfall
- State Mutations — *modifies* — shared state
- Forgetting to Return Dict Updates — *leads to* — old values
- Unhandled Exceptions — *causes* — workflow crashes
- Blocking Calls in Async Nodes — *blocks* — event loop
- `requests.get` — *is a* — Blocking Call
- `aiohttp.ClientSession` — *enables* — Non-blocking
- `aiohttp.ClientSession` — *used for* — async HTTP
- `try-except` block — *provides* — graceful fallback
- `logger.error` — *used in* — `try-except` block
- Slide 32 — *preceded by* — Slide 31
- Slide 32 — *part of* — Index
- Slide 32 — *followed by* — Slide 33
- `node` function — *operates on* — `state`

%% ai-graph-end %%