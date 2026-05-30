---
ai_hash: 5c262d2d2a5749c4
ai_model: google/gemini-2.5-flash
ai_updated: '2026-05-30'
entities:
- Slide 16
- Running the Graph
- Invoking the Workflow
- SmartTripPlanner
- trip_request
- initial_state
- planner.app.ainvoke
- result
- final
- research
- weather
- tool_calls
- asyncio
- Async Execution
- request
- budget
- Slide 15 - The Journey Plan Node (LLM Synthesis)
- Index
- Slide 17 - Observability & Tracing
slide_number: 16
source: https://github.com/trieu/ai-trip-planner/blob/main/docs/AGENTIC_PROGRAMMING_LECTURE.md
tags:
- lecture/slide
- langgraph
- agentic-programming
title: 'Slide 16: Running the Graph'
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

%% ai-graph-start %%

**Related notes:**
- [[Slide 22 - Integration Testing]]
- [[Slide 15 - The Journey Plan Node (LLM Synthesis)]]
- [[Slide 27 - Tool Calling in Nodes]]
- [[Appendix A - Full Trip Planner Code Example]]
- [[Slide 1 - Course Overview]]

**Relations:**
- Slide 16 — *has_title* — Running the Graph
- Slide 16 — *contains_section* — Invoking the Workflow
- Invoking the Workflow — *creates* — SmartTripPlanner
- Invoking the Workflow — *prepares* — trip_request
- Invoking the Workflow — *initializes* — initial_state
- Invoking the Workflow — *uses* — planner.app.ainvoke
- planner.app.ainvoke — *returns* — result
- result — *contains_field* — final
- result — *contains_field* — research
- result — *contains_field* — weather
- result — *contains_field* — tool_calls
- Slide 16 — *contains_section* — Async Execution
- Async Execution — *uses* — asyncio
- asyncio — *runs* — planner.app.ainvoke
- Slide 16 — *contains_section* — Try This
- Try This — *uses_input* — request
- Try This — *prints_field* — research
- Try This — *prints_field* — weather
- Try This — *prints_field* — budget
- Try This — *prints_field* — final
- Slide 16 — *previous_slide* — Slide 15 - The Journey Plan Node (LLM Synthesis)
- Slide 16 — *links_to* — Index
- Slide 16 — *next_slide* — Slide 17 - Observability & Tracing

%% ai-graph-end %%