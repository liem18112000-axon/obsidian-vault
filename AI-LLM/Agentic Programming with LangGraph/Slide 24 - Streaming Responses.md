---
ai_hash: c6869c659bb2545b
ai_model: google/gemini-2.5-flash
ai_updated: '2026-05-30'
entities:
- Slide 24
- Streaming Responses
- Long-running agents
- Progress
- FastAPI
- StreamingResponse (FastAPI class)
- plan_trip_stream (function)
- SmartTripPlanner (class)
- TripRequest (class)
- text/event-stream (media type)
- Slide 23 - Synthetic Evaluation
- Index
- Slide 25 - Deployment Patterns
slide_number: 24
source: https://github.com/trieu/ai-trip-planner/blob/main/docs/AGENTIC_PROGRAMMING_LECTURE.md
tags:
- lecture/slide
- langgraph
- agentic-programming
title: 'Slide 24: Streaming Responses'
---

# Slide 24: Streaming Responses

### Why Stream?

For long-running agents, stream responses to show progress:

```
User: "Plan my trip"
                    ↓ (1 sec)
System: "Loading your profile..."
                    ↓ (2 sec)
System: "Researching Tokyo..."
                    ↓ (2 sec)
System: "Checking weather..."
                    ↓ (1 sec)
System: "Calculating budget..."
                    ↓ (3 sec)
System: "Finalizing itinerary..."

[Complete itinerary appears]
```

### Implementation with FastAPI

```python
# backend/api/routes/trip_routes.py
from fastapi.responses import StreamingResponse
import asyncio
import json

@router.post("/trips/plan/stream")
async def plan_trip_stream(request: TripRequest):
    """Stream trip planning updates."""
    
    async def stream_generator():
        planner = SmartTripPlanner()
        initial_state = {"trip_request": request.dict(), "messages": [], "tool_calls": []}
        
        # Yield progress updates
        yield f"data: {json.dumps({'status': 'loading_profile'})}\n\n"
        
        # Run workflow
        async for state in planner.app.astream(initial_state):
            # Emit state updates
            yield f"data: {json.dumps({'state': state})}\n\n"
            await asyncio.sleep(0.1)  # Browser processing time
    
    return StreamingResponse(stream_generator(), media_type="text/event-stream")
```

---

← [[Slide 23 - Synthetic Evaluation|Previous]] · [[Index|🏠 Index]] · [[Slide 25 - Deployment Patterns|Next]] →

%% ai-graph-start %%

**Related notes:**
- [[Slide 16 - Running the Graph]]
- [[Slide 23 - Synthetic Evaluation]]
- [[Slide 22 - Integration Testing]]
- [[Slide 1 - Course Overview]]
- [[Slide 25 - Deployment Patterns]]

**Relations:**
- Slide 24 — *is titled* — Streaming Responses
- Streaming Responses — *are for* — Long-running agents
- Streaming Responses — *show* — Progress
- FastAPI — *is used for implementation of* — Streaming Responses
- StreamingResponse (FastAPI class) — *is a component of* — FastAPI
- plan_trip_stream (function) — *implements* — Streaming Responses
- plan_trip_stream (function) — *uses* — StreamingResponse (FastAPI class)
- plan_trip_stream (function) — *handles* — TripRequest (class)
- plan_trip_stream (function) — *uses* — SmartTripPlanner (class)
- StreamingResponse (FastAPI class) — *uses media type* — text/event-stream (media type)
- Slide 24 — *follows* — Slide 23 - Synthetic Evaluation
- Slide 24 — *is part of* — Index
- Slide 24 — *precedes* — Slide 25 - Deployment Patterns

%% ai-graph-end %%