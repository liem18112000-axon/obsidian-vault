---
title: "Slide 22: Integration Testing"
slide_number: 22
tags: [lecture/slide, langgraph, agentic-programming]
source: https://github.com/trieu/ai-trip-planner/blob/main/docs/AGENTIC_PROGRAMMING_LECTURE.md
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
