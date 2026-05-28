---
title: "Slide 18: Error Handling in Agentic Systems"
slide_number: 18
tags: [lecture/slide, langgraph, agentic-programming]
source: https://github.com/trieu/ai-trip-planner/blob/main/docs/AGENTIC_PROGRAMMING_LECTURE.md
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
