---
ai_hash: 6a7e44e00c23e8c8
ai_model: google/gemini-2.5-flash
ai_updated: '2026-05-30'
entities:
- Slide 5
- The Trip Planner Architecture
- The LangGraph Workflow
- load_profile
- Parallel Execution
- research_node
- weather_node
- budget_node
- aggregate
- journey_plan
- Research
- Weather
- Budget
- RAG
- web search
- Sequential fan-out
- parallel fan-out
- total latency
- LangGraph
- Slide 4 - LangGraph Core Concepts
- Index
- Slide 6 - State Definition in LangGraph
slide_number: 5
source: https://github.com/trieu/ai-trip-planner/blob/main/docs/AGENTIC_PROGRAMMING_LECTURE.md
tags:
- lecture/slide
- langgraph
- agentic-programming
title: 'Slide 5: The Trip Planner Architecture'
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

%% ai-graph-start %%

**Related notes:**
- [[Slide 31 - Scaling & Performance Optimization]]
- [[Slide 1 - Course Overview]]
- [[Slide 3 - The AI Trip Planner - Your Real-World Blueprint]]
- [[Slide 8 - Defining Edges (Control Flow)]]
- [[Slide 15 - The Journey Plan Node (LLM Synthesis)]]

**Relations:**
- Slide 5 — *describes* — The Trip Planner Architecture
- The Trip Planner Architecture — *uses* — The LangGraph Workflow
- The LangGraph Workflow — *starts with* — load_profile
- The LangGraph Workflow — *includes* — Parallel Execution
- The LangGraph Workflow — *includes* — aggregate
- The LangGraph Workflow — *ends with* — journey_plan
- Parallel Execution — *contains node* — research_node
- Parallel Execution — *contains node* — weather_node
- Parallel Execution — *contains node* — budget_node
- research_node — *performs* — Research
- weather_node — *performs* — Weather
- budget_node — *performs* — Budget
- Research — *involves* — RAG
- Research — *involves* — web search
- Research — *takes time* — 2s
- Weather — *takes time* — 1s
- Budget — *takes time* — 1s
- Sequential fan-out — *results in latency* — 4s
- parallel fan-out — *results in latency* — about 2s
- total latency — *includes* — load_profile
- total latency — *includes* — max(research, weather, budget)
- total latency — *includes* — aggregate
- total latency — *includes* — journey_plan
- Slide 5 — *follows* — Slide 4 - LangGraph Core Concepts
- Slide 5 — *precedes* — Slide 6 - State Definition in LangGraph
- Slide 5 — *is linked to* — Index
- The LangGraph Workflow — *is a concept of* — LangGraph

%% ai-graph-end %%