---
title: "Slide 5: The Trip Planner Architecture"
slide_number: 5
tags: [lecture/slide, langgraph, agentic-programming]
source: https://github.com/trieu/ai-trip-planner/blob/main/docs/AGENTIC_PROGRAMMING_LECTURE.md
---

# Slide 5: The Trip Planner Architecture

### The LangGraph Workflow

```
START
  ↓
load_profile (hydrate user preferences)
  ↓
┌─────────────────────────────────┐
│ Parallel Execution (Fan-Out)    │
├─────────────────────────────────┤
│ research_node                   │
│ weather_node                    │
│ budget_node                     │
└─────────────────────────────────┘
  ↓
aggregate (combine results)
  ↓
journey_plan (synthesize final output)
  ↓
END
```

### Why Parallel?

- **Research** may take 2s to call RAG/web search
- **Weather** may take 1s to fetch
- **Budget** may take 1s to calculate
- **Sequential fan-out = 4s**, **parallel fan-out = about 2s** ⚡

The full request still includes profile loading, aggregation, and final LLM synthesis:

```
total latency ≈ load_profile + max(research, weather, budget) + aggregate + journey_plan
```

---

← [[Slide 4 - LangGraph Core Concepts|Previous]] · [[Index|🏠 Index]] · [[Slide 6 - State Definition in LangGraph|Next]] →
