---
title: "Slide 16: Running the Graph"
slide_number: 16
tags: [lecture/slide, langgraph, agentic-programming]
source: https://github.com/trieu/ai-trip-planner/blob/main/docs/AGENTIC_PROGRAMMING_LECTURE.md
---

# Slide 16: Running the Graph

### Invoking the Workflow

```python
# Create planner
planner = SmartTripPlanner()

# Prepare input
trip_request = {
    "destination": "Tokyo, Japan",
    "duration": "7 days",
    "budget": "$2000",
    "interests": "food, culture, nightlife",
    "user_id": "user_123"
}

# Initialize state
initial_state = {
    "trip_request": trip_request,
    "messages": [],
    "tool_calls": []
}

# Run from inside an async function or notebook.
result = await planner.app.ainvoke(initial_state)

# Access outputs
print(result["final"])          # The final itinerary
print(result["research"])       # Research findings
print(result["weather"])        # Weather data
print(result["tool_calls"])     # For debugging/tracing
```

### Async Execution

```python
import asyncio

# Or use asyncio directly from a normal Python script.
result = asyncio.run(planner.app.ainvoke(initial_state))
```

### Try This

Create a tiny script that runs one request with:

```python
request = {
    "destination": "Da Nang, Vietnam",
    "duration": "3 days",
    "budget": "moderate",
    "interests": "food, beaches",
    "user_id": "demo_user_001",
}
```

Print only these fields first:

```python
print(result["research"])
print(result["weather"])
print(result["budget"])
```

After that works, print `result["final"]`.

---

← [[Slide 15 - The Journey Plan Node (LLM Synthesis)|Previous]] · [[Index|🏠 Index]] · [[Slide 17 - Observability & Tracing|Next]] →
