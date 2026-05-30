---
ai_hash: 28af2d22fe6d905f
ai_model: google/gemini-2.5-flash
ai_updated: '2026-05-30'
entities:
- Slide 11
- Research Node
- Tool Integration
- Node Responsibility
- Destination Insights
- Top attractions
- Local food
- Cultural events
- Hidden gems
- Implementation
- Python
- _research_node
- TripState
- get_destination_info
- TravelRAGService
- Tool Definition
- backend/tools/travel_tools.py
- langchain_core.tools
- tool
- Slide 10 - The Profile Node (Personalization)
- Profile Node
- Personalization
- Index
- Slide 12 - The Weather Node (Real-Time Data)
- Weather Node
- Real-Time Data
slide_number: 11
source: https://github.com/trieu/ai-trip-planner/blob/main/docs/AGENTIC_PROGRAMMING_LECTURE.md
tags:
- lecture/slide
- langgraph
- agentic-programming
title: 'Slide 11: The Research Node (Tool Integration)'
---

# Slide 11: The Research Node (Tool Integration)

### Node Responsibility

Gather detailed destination insights:
- Top attractions
- Local food
- Cultural events
- Hidden gems

### Implementation

```python
async def _research_node(self, state: TripState) -> Dict[str, Any]:
    dest = state["trip_request"]["destination"]
    calls = []
    
    try:
        # DIRECT TOOL CALL (no ToolNode wrapper)
        research_summary = await get_destination_info.ainvoke({
            "destination": dest
        })

        calls.append({
            "tool": "get_destination_info",
            "args": {"destination": dest}
        })
        
        return {
            "research": research_summary,
            "tool_calls": calls
        }
    
    except Exception as e:
        logger.error(f"[research_node] failed: {e}")
        return {
            "research": f"Unable to fetch research for {dest}",
            "tool_calls": []
        }
```

### Tool Definition

```python
# backend/tools/travel_tools.py
from langchain_core.tools import tool

@tool
async def get_destination_info(destination: str) -> str:
    """
    Fetch destination insights using the TravelRAGService.
    
    Args:
        destination: City or destination name
    
    Returns:
        Formatted destination insights
    """
    return await rag.get_destination_info(destination)
```

---

← [[Slide 10 - The Profile Node (Personalization)|Previous]] · [[Index|🏠 Index]] · [[Slide 12 - The Weather Node (Real-Time Data)|Next]] →

%% ai-graph-start %%

**Related notes:**
- [[Slide 27 - Tool Calling in Nodes]]
- [[Slide 36 - Tool Composition]]
- [[Slide 30 - RAG (Retrieval-Augmented Generation)]]
- [[Slide 12 - The Weather Node (Real-Time Data)]]
- [[Slide 15 - The Journey Plan Node (LLM Synthesis)]]

**Relations:**
- Slide 11 — *HAS_TITLE* — The Research Node (Tool Integration)
- Research Node — *IS_A* — Tool Integration
- Research Node — *HAS_RESPONSIBILITY* — Node Responsibility
- Node Responsibility — *INCLUDES* — Gather detailed destination insights
- Gather detailed destination insights — *COVERS* — Top attractions
- Gather detailed destination insights — *COVERS* — Local food
- Gather detailed destination insights — *COVERS* — Cultural events
- Gather detailed destination insights — *COVERS* — Hidden gems
- Research Node — *HAS_SECTION* — Implementation
- Implementation — *USES_LANGUAGE* — Python
- Implementation — *DEFINES_FUNCTION* — _research_node
- _research_node — *ACCEPTS_PARAMETER_TYPE* — TripState
- _research_node — *CALLS_TOOL* — get_destination_info
- Research Node — *HAS_SECTION* — Tool Definition
- Tool Definition — *LOCATED_AT* — backend/tools/travel_tools.py
- get_destination_info — *IS_DECORATED_BY* — tool
- tool — *FROM_MODULE* — langchain_core.tools
- get_destination_info — *FETCHES_INSIGHTS_USING* — TravelRAGService
- Slide 11 — *PREVIOUS_SLIDE* — Slide 10 - The Profile Node (Personalization)
- Slide 10 - The Profile Node (Personalization) — *IS_A* — Profile Node
- Profile Node — *IS_FOR* — Personalization
- Slide 11 — *NEXT_SLIDE* — Slide 12 - The Weather Node (Real-Time Data)
- Slide 12 - The Weather Node (Real-Time Data) — *IS_A* — Weather Node
- Weather Node — *IS_FOR* — Real-Time Data
- Slide 11 — *LINKS_TO* — Index

%% ai-graph-end %%