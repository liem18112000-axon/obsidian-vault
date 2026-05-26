---
title: "Slide 7: Building the Graph"
slide_number: 7
tags: [lecture/slide, langgraph, agentic-programming]
source: https://github.com/trieu/ai-trip-planner/blob/main/docs/AGENTIC_PROGRAMMING_LECTURE.md
---

# Slide 7: Building the Graph

### Step 1: Create the StateGraph

```python
from langgraph.graph import StateGraph, START, END
from core_llm.state_models import TripState

class SmartTripPlanner:
    def __init__(self):
        self.llm = MetaLLM.get_llm(temperature=0.7)
        self.profile_service = DataServiceFactory.get_service()
        self.app = self._build_graph()
    
    def _build_graph(self):
        # Create builder for TripState
        builder = StateGraph(TripState)
        
        # Add nodes (we'll define these next)
        builder.add_node("load_profile", self._profile_node)
        builder.add_node("research", self._research_node)
        builder.add_node("weather", self._weather_node)
        builder.add_node("budget", self._budget_node)
        builder.add_node("aggregate", self._aggregate_node)
        builder.add_node("journey_plan", self._journey_plan_node)
        
        return builder.compile()  # ← Magic happens here!
```

### What Does `compile()` Do?

Converts the graph definition into an executable state machine that:
- Manages state transitions
- Parallelizes where possible
- Validates graph structure
- Provides trace visibility

Your node code still owns runtime error handling with `try/except` and fallbacks.

---

← [[Slide 6 - State Definition in LangGraph|Previous]] · [[Index|🏠 Index]] · [[Slide 8 - Defining Edges (Control Flow)|Next]] →
