---
ai_hash: 896d6a08e37c8bdc
ai_model: google/gemini-2.5-flash
ai_updated: '2026-05-30'
entities:
- Slide 33
- Debugging Techniques
- Print-Based Debugging
- Logging with Levels
- Interactive Debugging (pdb)
- Observability Dashboard
- Python
- json
- logging
- pdb
- Arize Phoenix
- Slide 17
- node
- tool
- LLM prompts
- Slide 32 - Common Pitfalls & How to Avoid Them
- Index
- Slide 34 - Building Adaptive Agents
slide_number: 33
source: https://github.com/trieu/ai-trip-planner/blob/main/docs/AGENTIC_PROGRAMMING_LECTURE.md
tags:
- lecture/slide
- langgraph
- agentic-programming
title: 'Slide 33: Debugging Techniques'
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

%% ai-graph-start %%

**Related notes:**
- [[Slide 17 - Observability & Tracing]]
- [[Slide 40 - Recap & Your Turn]]
- [[Slide 18 - Error Handling in Agentic Systems]]
- [[Slide 32 - Common Pitfalls & How to Avoid Them]]
- [[Slide 9 - Writing Node Functions]]

**Relations:**
- Slide 33 — *HAS_TOPIC* — Debugging Techniques
- Debugging Techniques — *INCLUDES* — Print-Based Debugging
- Debugging Techniques — *INCLUDES* — Logging with Levels
- Debugging Techniques — *INCLUDES* — Interactive Debugging (pdb)
- Debugging Techniques — *INCLUDES* — Observability Dashboard
- Print-Based Debugging — *USES* — Python
- Print-Based Debugging — *USES* — json
- Logging with Levels — *USES* — Python
- Logging with Levels — *USES* — logging
- Interactive Debugging (pdb) — *USES* — Python
- Interactive Debugging (pdb) — *USES* — pdb
- Observability Dashboard — *REFERENCES* — Slide 17
- Observability Dashboard — *USES* — Arize Phoenix
- Observability Dashboard — *QUERIES* — node
- Observability Dashboard — *QUERIES* — tool
- Observability Dashboard — *QUERIES* — LLM prompts
- Slide 33 — *PREVIOUS_SLIDE* — Slide 32 - Common Pitfalls & How to Avoid Them
- Slide 33 — *NEXT_SLIDE* — Slide 34 - Building Adaptive Agents
- Slide 33 — *LINKS_TO* — Index

%% ai-graph-end %%