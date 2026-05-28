---
title: "Slide 6: State Definition in LangGraph"
slide_number: 6
tags: [lecture/slide, langgraph, agentic-programming]
source: https://github.com/trieu/ai-trip-planner/blob/main/docs/AGENTIC_PROGRAMMING_LECTURE.md
---

# Slide 6: State Definition in LangGraph

### What is State?

Shared data structure passed between all nodes. Think: "the trip planning context."

### Trip Planner State (from `state_models.py`)

```python
from typing import Annotated, List, Dict, Any, Optional
from typing_extensions import TypedDict
import operator
from langchain_core.messages import BaseMessage

class TripState(TypedDict):
    """Defines state passed between LangGraph nodes."""
    
    # Messages accumulate (operator.add means concatenate lists)
    messages: Annotated[List[BaseMessage], operator.add]
    
    # Trip request from the user
    trip_request: Dict[str, Any]  # {destination, budget, interests, user_id}
    
    # Hydrated user profile (from CDP/database)
    user_profile: Dict[str, Any]   # {preferences, past_trips, dietary_restrictions}
    
    # Location geocoding
    location_coords: Optional[Dict[str, float]]  # {lat, long}
    
    # Agent outputs
    research: Optional[str]        # Destination insights
    weather: Optional[str]         # Current weather
    budget: Optional[str]          # Cost breakdown
    final: Optional[str]           # Final itinerary
    
    # Tool calls (for observability)
    tool_calls: Annotated[List[Dict[str, Any]], operator.add]
```

### The `Annotated[List, operator.add]` Pattern

```python
# In your state model:
messages: Annotated[List[BaseMessage], operator.add]

# Node A returns one update:
def node_a(state):
    return {"messages": [msg1]}

# Node B returns another update:
def node_b(state):
    return {"messages": [msg2]}

# LangGraph applies the reducer:
# final messages = [msg1, msg2]
```

Important: reducers apply to **node return values**. Do not mutate `state["messages"]` in place.

### Try This

Open `backend/core_llm/state_models.py` and answer:

- Which fields can be updated safely by multiple nodes?
- Which fields would be overwritten if two nodes returned them at the same time?
- Why is `tool_calls` a list reducer instead of a plain list?

---

← [[Slide 5 - The Trip Planner Architecture|Previous]] · [[Index|🏠 Index]] · [[Slide 7 - Building the Graph|Next]] →
