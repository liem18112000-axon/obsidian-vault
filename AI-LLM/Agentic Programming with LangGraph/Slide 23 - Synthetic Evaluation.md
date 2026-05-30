---
ai_hash: 6030db9091d65b0c
ai_model: google/gemini-2.5-flash
ai_updated: '2026-05-30'
entities:
- Slide 23
- Synthetic Evaluation
- LLM outputs
- Quality
- Regressions
- Prompt changes
- Python script
- asyncio
- json
- SmartTripPlanner
- core_llm.smart_trip_planner
- Test cases
- Tokyo, Japan
- Paris, France
- Bali, Indonesia
- evaluation_results.json
- Slide 22 - Integration Testing
- Index
- Slide 24 - Streaming Responses
slide_number: 23
source: https://github.com/trieu/ai-trip-planner/blob/main/docs/AGENTIC_PROGRAMMING_LECTURE.md
tags:
- lecture/slide
- langgraph
- agentic-programming
title: 'Slide 23: Synthetic Evaluation'
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

%% ai-graph-start %%

**Related notes:**
- [[Slide 22 - Integration Testing]]
- [[Slide 21 - Testing Agentic Systems]]
- [[Slide 16 - Running the Graph]]
- [[Slide 24 - Streaming Responses]]
- [[Slide 15 - The Journey Plan Node (LLM Synthesis)]]

**Relations:**
- Slide 23 — *has title* — Synthetic Evaluation
- Synthetic Evaluation — *evaluates* — LLM outputs
- Synthetic Evaluation — *helps benchmark* — Quality
- Synthetic Evaluation — *helps detect* — Regressions
- Synthetic Evaluation — *helps A/B test* — Prompt changes
- Python script — *is located at* — backend/test_scripts/synthetic_data_gen.py
- Python script — *uses library* — asyncio
- Python script — *uses library* — json
- Python script — *imports class* — SmartTripPlanner
- SmartTripPlanner — *from module* — core_llm.smart_trip_planner
- Python script — *defines* — Test cases
- Test cases — *includes destination* — Tokyo, Japan
- Test cases — *includes destination* — Paris, France
- Test cases — *includes destination* — Bali, Indonesia
- Python script — *generates file* — evaluation_results.json
- Slide 23 — *precedes* — Slide 24 - Streaming Responses
- Slide 23 — *follows* — Slide 22 - Integration Testing
- Slide 23 — *links to* — Index

%% ai-graph-end %%