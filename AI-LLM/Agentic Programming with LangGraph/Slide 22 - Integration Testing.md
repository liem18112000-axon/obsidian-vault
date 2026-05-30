---
ai_hash: ba713ebb0f23bb60
ai_model: google/gemini-2.5-flash
ai_updated: '2026-05-30'
entities:
- Slide 22
- Integration Testing
- End-to-End Workflow Test
- pytest
- SmartTripPlanner
- Tokyo, Japan
- Slide 21
- Testing Agentic Systems
- Index
- Slide 23
- Synthetic Evaluation
slide_number: 22
source: https://github.com/trieu/ai-trip-planner/blob/main/docs/AGENTIC_PROGRAMMING_LECTURE.md
tags:
- lecture/slide
- langgraph
- agentic-programming
title: 'Slide 22: Integration Testing'
---

# Slide 22: Integration Testing

### End-to-End Workflow Test

```python
@pytest.mark.asyncio
async def test_full_trip_planner_workflow():
    """Test the complete graph execution."""
    planner = SmartTripPlanner()
    
    initial_state = {
        "trip_request": {
            "destination": "Tokyo, Japan",
            "duration": "3 days",
            "budget": "$1000",
            "interests": "food",
            "user_id": "test_user"
        },
        "messages": [],
        "tool_calls": [],
        "user_profile": {},
        "research": None,
        "weather": None,
        "budget": None,
        "final": None,
        "location_coords": None
    }
    
    result = await planner.app.ainvoke(initial_state)
    
    # Assertions
    assert result["final"] is not None
    assert len(result["final"]) > 0
    assert "Tokyo" in result["final"]
    assert len(result["tool_calls"]) > 0  # Tools were called
```

---

← [[Slide 21 - Testing Agentic Systems|Previous]] · [[Index|🏠 Index]] · [[Slide 23 - Synthetic Evaluation|Next]] →

%% ai-graph-start %%

**Related notes:**
- [[Slide 21 - Testing Agentic Systems]]
- [[Slide 16 - Running the Graph]]
- [[Slide 23 - Synthetic Evaluation]]
- [[Slide 27 - Tool Calling in Nodes]]
- [[Slide 1 - Course Overview]]

**Relations:**
- Slide 22 — *discusses* — Integration Testing
- Integration Testing — *includes* — End-to-End Workflow Test
- End-to-End Workflow Test — *uses* — pytest
- End-to-End Workflow Test — *tests* — SmartTripPlanner
- SmartTripPlanner — *processes destination* — Tokyo, Japan
- Slide 22 — *follows* — Slide 21
- Slide 21 — *is titled* — Testing Agentic Systems
- Slide 22 — *precedes* — Slide 23
- Slide 23 — *is titled* — Synthetic Evaluation
- Slide 22 — *links to* — Index

%% ai-graph-end %%