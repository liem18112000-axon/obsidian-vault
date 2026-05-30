---
ai_hash: 1b7ded30dbdd8b00
ai_model: google/gemini-2.5-flash
ai_updated: '2026-05-30'
entities:
- Slide 7
- Building the Graph
- StateGraph
- langgraph.graph
- START
- END
- TripState
- core_llm.state_models
- SmartTripPlanner
- MetaLLM
- DataServiceFactory
- _build_graph
- builder
- load_profile
- _profile_node
- research
- _research_node
- weather
- _weather_node
- budget
- _budget_node
- aggregate
- _aggregate_node
- journey_plan
- _journey_plan_node
- compile()
- executable state machine
- state transitions
- parallelization
- graph structure
- trace visibility
- runtime error handling
- try/except
- fallbacks
- Slide 6 - State Definition in LangGraph
- Index
- Slide 8 - Defining Edges (Control Flow)
slide_number: 7
source: https://github.com/trieu/ai-trip-planner/blob/main/docs/AGENTIC_PROGRAMMING_LECTURE.md
tags:
- lecture/slide
- langgraph
- agentic-programming
title: 'Slide 7: Building the Graph'
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

%% ai-graph-start %%

**Related notes:**
- [[Appendix B - Quick Reference]]
- [[Slide 8 - Defining Edges (Control Flow)]]
- [[Slide 1 - Course Overview]]
- [[Slide 4 - LangGraph Core Concepts]]
- [[Index]]

**Relations:**
- Slide 7 — *has_title* — Building the Graph
- Building the Graph — *details_step* — Create the StateGraph
- StateGraph — *imported_from* — langgraph.graph
- START — *imported_from* — langgraph.graph
- END — *imported_from* — langgraph.graph
- TripState — *imported_from* — core_llm.state_models
- SmartTripPlanner — *defines_method* — _build_graph
- SmartTripPlanner — *uses_component* — MetaLLM
- SmartTripPlanner — *uses_component* — DataServiceFactory
- _build_graph — *creates_instance* — builder
- builder — *is_instance_of* — StateGraph
- builder — *initializes_with_state* — TripState
- builder — *adds_node* — load_profile
- load_profile — *implemented_by* — _profile_node
- builder — *adds_node* — research
- research — *implemented_by* — _research_node
- builder — *adds_node* — weather
- weather — *implemented_by* — _weather_node
- builder — *adds_node* — budget
- budget — *implemented_by* — _budget_node
- builder — *adds_node* — aggregate
- aggregate — *implemented_by* — _aggregate_node
- builder — *adds_node* — journey_plan
- journey_plan — *implemented_by* — _journey_plan_node
- _build_graph — *calls_method* — compile()
- compile() — *converts_graph_to* — executable state machine
- executable state machine — *manages* — state transitions
- executable state machine — *supports* — parallelization
- executable state machine — *validates* — graph structure
- executable state machine — *provides* — trace visibility
- node code — *handles* — runtime error handling
- runtime error handling — *includes* — try/except
- runtime error handling — *includes* — fallbacks
- Slide 7 — *precedes* — Slide 8 - Defining Edges (Control Flow)
- Slide 7 — *follows* — Slide 6 - State Definition in LangGraph
- Slide 7 — *part_of* — Index

%% ai-graph-end %%