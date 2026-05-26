---
title: "Slide 19: Conditional Branching (Advanced)"
slide_number: 19
tags: [lecture/slide, langgraph, agentic-programming]
source: https://github.com/trieu/ai-trip-planner/blob/main/docs/AGENTIC_PROGRAMMING_LECTURE.md
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
