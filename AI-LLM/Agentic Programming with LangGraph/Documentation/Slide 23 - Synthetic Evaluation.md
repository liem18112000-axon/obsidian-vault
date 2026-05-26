---
title: "Slide 23: Synthetic Evaluation"
slide_number: 23
tags: [lecture/slide, langgraph, agentic-programming]
source: https://github.com/trieu/ai-trip-planner/blob/main/docs/AGENTIC_PROGRAMMING_LECTURE.md
---

# Slide 23: Synthetic Evaluation

### Why Evaluation Matters

LLM outputs are subjective. Use synthetic evaluations to:
- Benchmark quality
- Detect regressions
- A/B test prompt changes

```python
# backend/test_scripts/synthetic_data_gen.py
import asyncio
import json
from core_llm.smart_trip_planner import SmartTripPlanner

# Test cases
test_destinations = [
    {"destination": "Tokyo, Japan", "duration": "7 days", "budget": "$2000"},
    {"destination": "Paris, France", "duration": "5 days", "budget": "$1500"},
    {"destination": "Bali, Indonesia", "duration": "10 days", "budget": "$800"},
]

async def evaluate_planner():
    planner = SmartTripPlanner()
    results = []
    
    for test_case in test_destinations:
        initial_state = {
            "trip_request": test_case,
            "messages": [],
            "tool_calls": [],
            "user_profile": {}
        }
        
        result = await planner.app.ainvoke(initial_state)
        
        # Store result with timing
        results.append({
            "test": test_case,
            "final": result["final"],
            "tool_calls_count": len(result["tool_calls"]),
            "status": "success"
        })
    
    with open("evaluation_results.json", "w") as f:
        json.dump(results, f, indent=2)

asyncio.run(evaluate_planner())
```

---

← [[Slide 22 - Integration Testing|Previous]] · [[Index|🏠 Index]] · [[Slide 24 - Streaming Responses|Next]] →
