---
title: "Slide 21: Testing Agentic Systems"
slide_number: 21
tags: [lecture/slide, langgraph, agentic-programming]
source: https://github.com/trieu/ai-trip-planner/blob/main/docs/AGENTIC_PROGRAMMING_LECTURE.md
---

# Slide 21: Testing Agentic Systems

### Unit Testing Nodes

```python
# backend/tests/test_trip_planner.py
import pytest
from unittest.mock import Mock, patch
from core_llm.smart_trip_planner import SmartTripPlanner
from core_llm.state_models import TripState

@pytest.fixture
def planner():
    return SmartTripPlanner()

def test_profile_node(planner):
    """Test profile loading."""
    state = TripState(
        trip_request={"user_id": "test_user", "interests": "food, culture"},
        messages=[],
        tool_calls=[]
    )
    
    result = planner._profile_node(state)
    
    assert "user_profile" in result
    assert "current_interests" in result["user_profile"]
    assert "food" in result["user_profile"]["current_interests"]

@pytest.mark.asyncio
async def test_research_node_with_mock_tool(planner):
    """Test research with mocked tool."""
    with patch("tools.travel_tools.get_destination_info") as mock_tool:
        mock_tool.ainvoke = AsyncMock(return_value="Tokyo is...")
        
        state = TripState(
            trip_request={"destination": "Tokyo, Japan"},
            user_profile={},
            messages=[],
            tool_calls=[]
        )
        
        result = await planner._research_node(state)
        
        assert result["research"] == "Tokyo is..."
        assert len(result["tool_calls"]) == 1
```

---

← [[Slide 20 - State Mutations & Immutability|Previous]] · [[Index|🏠 Index]] · [[Slide 22 - Integration Testing|Next]] →
