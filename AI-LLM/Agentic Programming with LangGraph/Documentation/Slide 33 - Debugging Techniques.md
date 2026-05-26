---
title: "Slide 33: Debugging Techniques"
slide_number: 33
tags: [lecture/slide, langgraph, agentic-programming]
source: https://github.com/trieu/ai-trip-planner/blob/main/docs/AGENTIC_PROGRAMMING_LECTURE.md
---

# Slide 33: Debugging Techniques

### 1. Print-Based Debugging

```python
def debug_node(state):
    print(f"Input state: {json.dumps(state, indent=2, default=str)}")
    
    result = some_computation(state)
    
    print(f"Output: {result}")
    return result
```

### 2. Logging with Levels

```python
import logging

logger = logging.getLogger(__name__)
logger.setLevel(logging.DEBUG)

def node(state):
    logger.debug(f"Processing state: {state['trip_request']}")
    logger.info("Starting research node")
    
    try:
        result = risky_operation()
        logger.info(f"Success: {result}")
    except Exception as e:
        logger.error(f"Failed: {e}", exc_info=True)
    
    return {"result": result}
```

### 3. Interactive Debugging (pdb)

```python
def node(state):
    import pdb
    pdb.set_trace()  # Pauses execution
    
    # Now you can inspect:
    # (Pdb) print(state["trip_request"])
    # (Pdb) n (next)
```

### 4. Observability Dashboard

```python
# See Slide 17 - use Arize Phoenix traces
# Query:
# - Which node is slow?
# - Which tool failed?
# - What LLM prompts generated bad outputs?
```

---

← [[Slide 32 - Common Pitfalls & How to Avoid Them|Previous]] · [[Index|🏠 Index]] · [[Slide 34 - Building Adaptive Agents|Next]] →
