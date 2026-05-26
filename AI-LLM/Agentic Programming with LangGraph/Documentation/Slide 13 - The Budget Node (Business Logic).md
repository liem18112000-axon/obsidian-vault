---
title: "Slide 13: The Budget Node (Business Logic)"
slide_number: 13
tags: [lecture/slide, langgraph, agentic-programming]
source: https://github.com/trieu/ai-trip-planner/blob/main/docs/AGENTIC_PROGRAMMING_LECTURE.md
---

# Slide 13: The Budget Node (Business Logic)

### Node Responsibility

Fetch or estimate travel costs for the selected budget level:
- Accommodation
- Food
- Activities
- Transportation

The final itinerary prompt can then turn those costs into a day-by-day plan.

### Implementation

```python
async def _budget_node(self, state: TripState) -> Dict[str, Any]:
    trip_req = state["trip_request"]
    destination = trip_req.get("destination")
    budget = trip_req.get("budget")
    
    try:
        costs = await get_costs.ainvoke({
            "destination": destination,
            "budget_level": budget
        })
        
        return {
            "budget": costs,
            "tool_calls": [{
                "tool": "get_costs",
                "args": {"destination": destination, "budget_level": budget}
            }]
        }
    
    except Exception as e:
        logger.error(f"[budget_node] failed: {e}")
        return {
            "budget": f"Budget analysis unavailable",
            "tool_calls": []
        }
```

---

← [[Slide 12 - The Weather Node (Real-Time Data)|Previous]] · [[Index|🏠 Index]] · [[Slide 14 - The Aggregate Node (Data Fusion)|Next]] →
