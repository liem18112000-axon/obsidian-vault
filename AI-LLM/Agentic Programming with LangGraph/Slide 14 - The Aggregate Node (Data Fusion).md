---
ai_hash: 93770dca60276a68
ai_model: google/gemini-2.5-flash
ai_updated: '2026-05-30'
entities:
- Slide 14
- The Aggregate Node
- Data Fusion
- Aggregation
- parallel nodes
- coherent state
- final synthesis
- TripState
- research
- weather
- budget
- user_profile
- tool_calls
- deduplicate_tool_calls
- metadata
- journey planner
- shared state
- downstream consumption
- failed nodes
- redundant tool call metadata
- Slide 13
- The Budget Node
- Business Logic
- Index
- Slide 15
- The Journey Plan Node
- LLM Synthesis
- _aggregate_node
slide_number: 14
source: https://github.com/trieu/ai-trip-planner/blob/main/docs/AGENTIC_PROGRAMMING_LECTURE.md
tags:
- lecture/slide
- langgraph
- agentic-programming
title: 'Slide 14: The Aggregate Node (Data Fusion)'
---

# Slide 14: The Aggregate Node (Data Fusion)

### Role of Aggregation

Combine outputs from parallel nodes into coherent state for final synthesis:

```python
def _aggregate_node(self, state: TripState) -> Dict[str, Any]:
    # All parallel nodes have completed
    # Now we have research, weather, budget, user_profile
    
    # This project keeps the original state fields and only cleans metadata.
    # The journey planner reads research/weather/budget directly from state.
    cleaned_calls = deduplicate_tool_calls(state.get("tool_calls", []))
    
    return {"tool_calls": cleaned_calls}
```

### What Happens Here?

1. ✅ Wait for all parallel nodes to finish
2. ✅ Receive partial fallbacks from failed nodes
3. ✅ Deduplicate redundant tool call metadata
4. ✅ Preserve the shared state for downstream consumption
5. ✅ Let the journey planner read `research`, `weather`, and `budget`

---

← [[Slide 13 - The Budget Node (Business Logic)|Previous]] · [[Index|🏠 Index]] · [[Slide 15 - The Journey Plan Node (LLM Synthesis)|Next]] →

%% ai-graph-start %%

**Related notes:**
- [[Slide 15 - The Journey Plan Node (LLM Synthesis)]]
- [[Slide 13 - The Budget Node (Business Logic)]]
- [[Slide 12 - The Weather Node (Real-Time Data)]]
- [[Slide 9 - Writing Node Functions]]
- [[Slide 1 - Course Overview]]

**Relations:**
- Slide 14 — *is about* — The Aggregate Node
- The Aggregate Node — *is also known as* — Data Fusion
- The Aggregate Node — *has role* — Aggregation
- Aggregation — *combines outputs from* — parallel nodes
- Aggregation — *produces* — coherent state
- coherent state — *is for* — final synthesis
- The Aggregate Node — *processes* — TripState
- TripState — *contains* — research
- TripState — *contains* — weather
- TripState — *contains* — budget
- TripState — *contains* — user_profile
- The Aggregate Node — *cleans* — metadata
- The Aggregate Node — *uses* — deduplicate_tool_calls
- deduplicate_tool_calls — *processes* — tool_calls
- The Aggregate Node — *waits for* — parallel nodes
- The Aggregate Node — *receives fallbacks from* — failed nodes
- The Aggregate Node — *deduplicates* — redundant tool call metadata
- The Aggregate Node — *preserves* — shared state
- shared state — *is for* — downstream consumption
- journey planner — *reads* — research
- journey planner — *reads* — weather
- journey planner — *reads* — budget
- Slide 14 — *precedes* — Slide 15
- Slide 15 — *is about* — The Journey Plan Node
- The Journey Plan Node — *performs* — LLM Synthesis
- Slide 14 — *follows* — Slide 13
- Slide 13 — *is about* — The Budget Node
- The Budget Node — *involves* — Business Logic
- Slide 14 — *links to* — Index
- Slide 14 — *links to* — Slide 13
- Slide 14 — *links to* — Slide 15
- The Aggregate Node — *implemented by function* — _aggregate_node
- _aggregate_node — *takes as input* — TripState
- _aggregate_node — *calls* — deduplicate_tool_calls

%% ai-graph-end %%