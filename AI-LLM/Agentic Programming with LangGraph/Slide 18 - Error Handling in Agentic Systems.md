---
ai_hash: 4a1541589509d167
ai_model: google/gemini-2.5-flash
ai_updated: '2026-05-30'
entities:
- Slide 18
- Error Handling
- Agentic Systems
- Patterns for Resilience
- Try-Catch with Fallbacks
- Conditional Edges (Branching)
- _research_node
- TripState
- get_destination_info
- TimeoutError
- logger
- HumanMessage
- Exception
- _should_use_rag
- builder
- rag
- llm
- rag_node
- llm_node
- Slide 17 - Observability & Tracing
- Index
- Slide 19 - Conditional Branching (Advanced)
slide_number: 18
source: https://github.com/trieu/ai-trip-planner/blob/main/docs/AGENTIC_PROGRAMMING_LECTURE.md
tags:
- lecture/slide
- langgraph
- agentic-programming
title: 'Slide 18: Error Handling in Agentic Systems'
---

# Slide 18: Error Handling in Agentic Systems

### Patterns for Resilience

#### 1. Try-Catch with Fallbacks

```python
async def _research_node(self, state: TripState) -> Dict[str, Any]:
    dest = state["trip_request"]["destination"]
    
    try:
        # Try primary tool
        research = await get_destination_info.ainvoke({"destination": dest})
    
    except TimeoutError:
        logger.warning(f"Web search timeout for {dest}, using LLM fallback")
        # Fallback: use LLM reasoning instead
        research = await self.llm.ainvoke([
            HumanMessage(f"Generate travel insights for {dest}")
        ])
    
    except Exception as e:
        logger.error(f"[research_node] unknown error: {e}")
        research = f"Research unavailable (system will continue with other data)"
    
    return {"research": research}
```

#### 2. Conditional Edges (Branching)

```python
def _should_use_rag(state: TripState) -> str:
    """Route to RAG or LLM based on data availability."""
    if state.get("research") and len(state["research"]) > 100:
        return "rag"  # Use vector DB search
    else:
        return "llm"  # Fall back to LLM reasoning

builder.add_conditional_edges(
    "research",
    _should_use_rag,
    {"rag": "rag_node", "llm": "llm_node"}
)
```

---

← [[Slide 17 - Observability & Tracing|Previous]] · [[Index|🏠 Index]] · [[Slide 19 - Conditional Branching (Advanced)|Next]] →

%% ai-graph-start %%

**Related notes:**
- [[Slide 19 - Conditional Branching (Advanced)]]
- [[Slide 34 - Building Adaptive Agents]]
- [[Index]]
- [[Slide 12 - The Weather Node (Real-Time Data)]]
- [[Slide 1 - Course Overview]]

**Relations:**
- Slide 18 — *discusses* — Error Handling
- Error Handling — *occurs in* — Agentic Systems
- Error Handling — *employs* — Patterns for Resilience
- Patterns for Resilience — *includes* — Try-Catch with Fallbacks
- Patterns for Resilience — *includes* — Conditional Edges (Branching)
- Try-Catch with Fallbacks — *is demonstrated by* — _research_node
- _research_node — *takes* — TripState
- _research_node — *invokes* — get_destination_info
- _research_node — *handles* — TimeoutError
- _research_node — *uses* — logger
- _research_node — *uses* — HumanMessage
- _research_node — *handles* — Exception
- Conditional Edges (Branching) — *is demonstrated by* — _should_use_rag
- _should_use_rag — *takes* — TripState
- _should_use_rag — *returns* — rag
- _should_use_rag — *returns* — llm
- builder — *adds* — Conditional Edges (Branching)
- Conditional Edges (Branching) — *routes to* — rag_node
- Conditional Edges (Branching) — *routes to* — llm_node
- Slide 18 — *follows* — Slide 17 - Observability & Tracing
- Slide 18 — *precedes* — Slide 19 - Conditional Branching (Advanced)
- Slide 18 — *is linked from* — Index

%% ai-graph-end %%