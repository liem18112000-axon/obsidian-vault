---
title: "Slide 11: The Research Node (Tool Integration)"
slide_number: 11
tags: [lecture/slide, langgraph, agentic-programming]
source: https://github.com/trieu/ai-trip-planner/blob/main/docs/AGENTIC_PROGRAMMING_LECTURE.md
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
