---
ai_hash: d3002a9c0e654b0f
ai_model: google/gemini-2.5-flash
ai_updated: '2026-05-30'
entities:
- Slide 8
- Edges
- Control Flow
- Types of Edges
- START
- load_profile
- Sequential
- Implicit Parallelism
- research
- weather
- budget
- Converging Paths
- aggregate
- journey_plan
- END
- builder.add_edge
- backend/core_llm/smart_trip_planner.py
- _build_graph
- Slide 7 - Building the Graph
- Index
- Slide 9 - Writing Node Functions
slide_number: 8
source: https://github.com/trieu/ai-trip-planner/blob/main/docs/AGENTIC_PROGRAMMING_LECTURE.md
tags:
- lecture/slide
- langgraph
- agentic-programming
title: 'Slide 8: Defining Edges (Control Flow)'
---

# Slide 8: Defining Edges (Control Flow)

### Types of Edges

```python
# 1. START → First Node
builder.add_edge(START, "load_profile")

# 2. Sequential: A → B
builder.add_edge("load_profile", "research")

# 3. Implicit Parallelism (multiple edges from same node)
builder.add_edge("load_profile", "research")
builder.add_edge("load_profile", "weather")
builder.add_edge("load_profile", "budget")
# ← These all run in parallel!

# 4. Converging Paths
builder.add_edge("research", "aggregate")
builder.add_edge("weather", "aggregate")
builder.add_edge("budget", "aggregate")
# ← aggregate waits for all three to complete

# 5. End Node
builder.add_edge("journey_plan", END)
```

### Visualization

```
START
  ↓
load_profile ──→ research ──┐
  ├──→ weather ─────┼─→ aggregate → journey_plan → END
  └──→ budget ──────┘
```

### Try This

Open `backend/core_llm/smart_trip_planner.py` and find `_build_graph`.

Then make a mental trace:

1. What node runs first?
2. What three nodes run after `load_profile`?
3. What node waits for those three to complete?
4. Where does the final response get written?

---

← [[Slide 7 - Building the Graph|Previous]] · [[Index|🏠 Index]] · [[Slide 9 - Writing Node Functions|Next]] →

%% ai-graph-start %%

**Related notes:**
- [[Slide 7 - Building the Graph]]
- [[Slide 1 - Course Overview]]
- [[Slide 16 - Running the Graph]]
- [[Slide 5 - The Trip Planner Architecture]]
- [[Appendix B - Quick Reference]]

**Relations:**
- Slide 8 — *discusses* — Edges
- Slide 8 — *discusses* — Control Flow
- Edges — *include type* — Sequential
- Edges — *include type* — Implicit Parallelism
- Edges — *include type* — Converging Paths
- builder.add_edge — *defines edge* — START
- START — *connects to* — load_profile
- builder.add_edge — *defines edge* — load_profile
- load_profile — *connects to* — research
- builder.add_edge — *defines edge* — load_profile
- load_profile — *connects to* — weather
- builder.add_edge — *defines edge* — load_profile
- load_profile — *connects to* — budget
- load_profile — *triggers parallel execution of* — research
- load_profile — *triggers parallel execution of* — weather
- load_profile — *triggers parallel execution of* — budget
- builder.add_edge — *defines edge* — research
- research — *connects to* — aggregate
- builder.add_edge — *defines edge* — weather
- weather — *connects to* — aggregate
- builder.add_edge — *defines edge* — budget
- budget — *connects to* — aggregate
- aggregate — *requires completion of* — research
- aggregate — *requires completion of* — weather
- aggregate — *requires completion of* — budget
- builder.add_edge — *defines edge* — journey_plan
- journey_plan — *connects to* — END
- backend/core_llm/smart_trip_planner.py — *contains function* — _build_graph
- Slide 8 — *follows* — Slide 7 - Building the Graph
- Slide 8 — *precedes* — Slide 9 - Writing Node Functions
- Slide 8 — *is part of* — Index

%% ai-graph-end %%