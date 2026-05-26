---
title: "Slide 14: The Aggregate Node (Data Fusion)"
slide_number: 14
tags: [lecture/slide, langgraph, agentic-programming]
source: https://github.com/trieu/ai-trip-planner/blob/main/docs/AGENTIC_PROGRAMMING_LECTURE.md
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
