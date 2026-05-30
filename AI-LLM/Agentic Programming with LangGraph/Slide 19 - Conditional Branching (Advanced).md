---
ai_hash: 8e84a0b97c7018a5
ai_model: google/gemini-2.5-flash
ai_updated: '2026-05-30'
entities:
- Slide 19
- Conditional Branching
- Advanced
- Users
- Agents
- _route_to_agent
- TripState
- duration
- budget (parameter)
- detailed_planner
- quick_planner
- builder
- add_conditional_edges
- load_profile
- detailed_planning
- research
- weather
- budget (step)
- aggregate
- quick_planning
- simple_research
- simple_plan
- Graph with Branches
- Slide 18
- Error Handling in Agentic Systems
- Slide 20
- State Mutations & Immutability
- trip complexity
- START
- END
slide_number: 19
source: https://github.com/trieu/ai-trip-planner/blob/main/docs/AGENTIC_PROGRAMMING_LECTURE.md
tags:
- lecture/slide
- langgraph
- agentic-programming
title: 'Slide 19: Conditional Branching (Advanced)'
---

# Slide 19: Conditional Branching (Advanced)

### When Decisions Matter

Different users might need different agents:

```python
def _route_to_agent(state: TripState) -> str:
    """Route based on trip complexity."""
    duration = int(state["trip_request"].get("duration", "0").split()[0])
    budget = float(state["trip_request"].get("budget", "$0").replace("$", ""))
    
    # Complex trip (long, high budget) → extra planning
    if duration > 14 and budget > 5000:
        return "detailed_planner"
    
    # Simple trip → quick planner
    else:
        return "quick_planner"

builder.add_conditional_edges(
    "load_profile",
    _route_to_agent,
    {
        "detailed_planner": "detailed_planning",
        "quick_planner": "quick_planning"
    }
)
```

### Graph with Branches

```
START
  ↓
load_profile
  ↓
[conditional: duration/budget?]
  ├→ detailed_planning → research → weather → budget → aggregate → END
  └→ quick_planning → simple_research → simple_plan → END
```

---

← [[Slide 18 - Error Handling in Agentic Systems|Previous]] · [[Index|🏠 Index]] · [[Slide 20 - State Mutations & Immutability|Next]] →

%% ai-graph-start %%

**Related notes:**
- [[Slide 18 - Error Handling in Agentic Systems]]
- [[Slide 34 - Building Adaptive Agents]]
- [[Index]]
- [[Slide 35 - Agent Chains vs Agent Trees]]
- [[Slide 8 - Defining Edges (Control Flow)]]

**Relations:**
- Slide 19 — *discusses* — Conditional Branching
- Conditional Branching — *is a type of* — Advanced
- Conditional Branching — *enables routing to different* — Agents
- _route_to_agent — *is a* — function
- _route_to_agent — *takes input* — TripState
- _route_to_agent — *routes based on* — trip complexity
- TripState — *contains* — duration
- TripState — *contains* — budget (parameter)
- _route_to_agent — *returns* — detailed_planner
- _route_to_agent — *returns* — quick_planner
- builder — *uses* — add_conditional_edges
- add_conditional_edges — *configures routing from* — load_profile
- add_conditional_edges — *uses routing function* — _route_to_agent
- add_conditional_edges — *maps route* — detailed_planner
- detailed_planner — *maps to node* — detailed_planning
- add_conditional_edges — *maps route* — quick_planner
- quick_planner — *maps to node* — quick_planning
- Graph with Branches — *illustrates* — Conditional Branching
- START — *leads to* — load_profile
- load_profile — *branches conditionally to* — detailed_planning
- load_profile — *branches conditionally to* — quick_planning
- detailed_planning — *leads to* — research
- research — *leads to* — weather
- weather — *leads to* — budget (step)
- budget (step) — *leads to* — aggregate
- aggregate — *leads to* — END
- quick_planning — *leads to* — simple_research
- simple_research — *leads to* — simple_plan
- simple_plan — *leads to* — END
- Slide 19 — *follows* — Slide 18
- Slide 18 — *is titled* — Error Handling in Agentic Systems
- Slide 19 — *precedes* — Slide 20
- Slide 20 — *is titled* — State Mutations & Immutability

%% ai-graph-end %%