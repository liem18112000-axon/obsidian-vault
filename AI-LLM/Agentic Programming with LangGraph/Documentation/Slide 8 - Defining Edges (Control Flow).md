---
title: "Slide 8: Defining Edges (Control Flow)"
slide_number: 8
tags: [lecture/slide, langgraph, agentic-programming]
source: https://github.com/trieu/ai-trip-planner/blob/main/docs/AGENTIC_PROGRAMMING_LECTURE.md
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
