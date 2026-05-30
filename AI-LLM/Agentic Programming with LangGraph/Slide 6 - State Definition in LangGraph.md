---
ai_hash: 6f7687f000a7a46a
ai_model: google/gemini-2.5-flash
ai_updated: '2026-05-30'
entities:
- Slide 6
- State Definition
- LangGraph
- State
- Shared data structure
- Nodes
- Trip Planner State
- '`state_models.py`'
- '`TripState`'
- messages
- '`Annotated`'
- '`List`'
- '`operator.add`'
- '`BaseMessage`'
- trip_request
- user_profile
- CDP
- database
- location_coords
- research
- weather
- budget
- final itinerary
- tool_calls
- observability
- reducer
- node return values
- '`backend/core_llm/state_models.py`'
- Slide 5
- Slide 7
- Index
slide_number: 6
source: https://github.com/trieu/ai-trip-planner/blob/main/docs/AGENTIC_PROGRAMMING_LECTURE.md
tags:
- lecture/slide
- langgraph
- agentic-programming
title: 'Slide 6: State Definition in LangGraph'
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

%% ai-graph-start %%

**Related notes:**
- [[Slide 1 - Course Overview]]
- [[Slide 9 - Writing Node Functions]]
- [[Appendix B - Quick Reference]]
- [[Slide 7 - Building the Graph]]
- [[Slide 20 - State Mutations & Immutability]]

**Relations:**
- Slide 6 — *discusses* — State Definition
- State Definition — *is_in* — LangGraph
- State — *is_a* — Shared data structure
- State — *is_passed_between* — Nodes
- Trip Planner State — *is_an_example_of* — State
- Trip Planner State — *is_defined_by* — `TripState`
- `TripState` — *is_defined_in* — `state_models.py`
- `state_models.py` — *is_located_at* — `backend/core_llm/state_models.py`
- `TripState` — *includes_field* — messages
- `TripState` — *includes_field* — trip_request
- `TripState` — *includes_field* — user_profile
- `TripState` — *includes_field* — location_coords
- `TripState` — *includes_field* — research
- `TripState` — *includes_field* — weather
- `TripState` — *includes_field* — budget
- `TripState` — *includes_field* — final itinerary
- `TripState` — *includes_field* — tool_calls
- messages — *uses_pattern* — `Annotated[List, operator.add]`
- tool_calls — *uses_pattern* — `Annotated[List, operator.add]`
- `Annotated[List, operator.add]` — *uses* — `operator.add`
- `operator.add` — *is_a* — reducer
- reducer — *applies_to* — node return values
- user_profile — *sourced_from* — CDP
- user_profile — *sourced_from* — database
- tool_calls — *is_for* — observability
- Slide 6 — *follows* — Slide 5
- Slide 6 — *precedes* — Slide 7
- Slide 6 — *is_part_of* — Index

%% ai-graph-end %%