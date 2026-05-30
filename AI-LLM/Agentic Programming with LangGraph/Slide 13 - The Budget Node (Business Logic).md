---
ai_hash: 4b7829ed07b170e6
ai_model: google/gemini-2.5-flash
ai_updated: '2026-05-30'
entities:
- Slide 13
- Budget Node
- Business Logic
- Node Responsibility
- Travel Costs
- Accommodation
- Food
- Activities
- Transportation
- Itinerary Prompt
- Day-by-day Plan
- Implementation
- _budget_node
- TripState
- get_costs
- Destination
- Budget Level
- Tool Calls
- Slide 12
- Weather Node
- Real-Time Data
- Index
- Slide 14
- Aggregate Node
- Data Fusion
slide_number: 13
source: https://github.com/trieu/ai-trip-planner/blob/main/docs/AGENTIC_PROGRAMMING_LECTURE.md
tags:
- lecture/slide
- langgraph
- agentic-programming
title: 'Slide 13: The Budget Node (Business Logic)'
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

%% ai-graph-start %%

**Related notes:**
- [[Slide 15 - The Journey Plan Node (LLM Synthesis)]]
- [[Slide 14 - The Aggregate Node (Data Fusion)]]
- [[Slide 12 - The Weather Node (Real-Time Data)]]
- [[Slide 28 - Prompt Engineering for Agentic Systems]]
- [[Slide 16 - Running the Graph]]

**Relations:**
- Slide 13 — *describes* — Budget Node
- Budget Node — *handles* — Business Logic
- Budget Node — *has responsibility* — Node Responsibility
- Node Responsibility — *involves estimating* — Travel Costs
- Travel Costs — *include* — Accommodation
- Travel Costs — *include* — Food
- Travel Costs — *include* — Activities
- Travel Costs — *include* — Transportation
- Itinerary Prompt — *uses* — Travel Costs
- Itinerary Prompt — *generates* — Day-by-day Plan
- _budget_node — *is implementation of* — Budget Node
- _budget_node — *takes input* — TripState
- _budget_node — *invokes* — get_costs
- get_costs — *requires argument* — Destination
- get_costs — *requires argument* — Budget Level
- _budget_node — *returns* — Travel Costs
- _budget_node — *returns* — Tool Calls
- Slide 13 — *preceded by* — Slide 12
- Slide 12 — *describes* — Weather Node
- Weather Node — *handles* — Real-Time Data
- Slide 13 — *followed by* — Slide 14
- Slide 14 — *describes* — Aggregate Node
- Aggregate Node — *performs* — Data Fusion
- Slide 13 — *links to* — Index

%% ai-graph-end %%