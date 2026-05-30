---
ai_hash: 8831d42cae8aed78
ai_model: google/gemini-2.5-flash
ai_updated: '2026-05-30'
entities:
- Slide 21
- Testing Agentic Systems
- Unit Testing Nodes
- pytest
- unittest.mock
- Mock
- patch
- SmartTripPlanner
- TripState
- _profile_node
- _research_node
- get_destination_info
- AsyncMock
- Slide 20 - State Mutations & Immutability
- Index
- Slide 22 - Integration Testing
slide_number: 21
source: https://github.com/trieu/ai-trip-planner/blob/main/docs/AGENTIC_PROGRAMMING_LECTURE.md
tags:
- lecture/slide
- langgraph
- agentic-programming
title: 'Slide 21: Testing Agentic Systems'
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

%% ai-graph-start %%

**Related notes:**
- [[Slide 22 - Integration Testing]]
- [[Slide 23 - Synthetic Evaluation]]
- [[Slide 1 - Course Overview]]
- [[Slide 27 - Tool Calling in Nodes]]
- [[Slide 9 - Writing Node Functions]]

**Relations:**
- Slide 21 — *is titled* — Testing Agentic Systems
- Testing Agentic Systems — *includes section* — Unit Testing Nodes
- Unit Testing Nodes — *demonstrates use of* — pytest
- Unit Testing Nodes — *demonstrates use of* — unittest.mock
- unittest.mock — *provides* — Mock
- unittest.mock — *provides* — patch
- Unit Testing Nodes — *tests component* — SmartTripPlanner
- SmartTripPlanner — *operates on* — TripState
- Unit Testing Nodes — *tests method* — _profile_node
- _profile_node — *is a method of* — SmartTripPlanner
- Unit Testing Nodes — *tests method* — _research_node
- _research_node — *is a method of* — SmartTripPlanner
- test_research_node_with_mock_tool — *mocks* — get_destination_info
- test_research_node_with_mock_tool — *uses* — patch
- test_research_node_with_mock_tool — *uses* — AsyncMock
- Slide 21 — *follows* — Slide 20 - State Mutations & Immutability
- Slide 21 — *is part of* — Index
- Slide 21 — *precedes* — Slide 22 - Integration Testing

%% ai-graph-end %%