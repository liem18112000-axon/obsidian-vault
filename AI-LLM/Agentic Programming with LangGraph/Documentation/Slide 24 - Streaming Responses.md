---
title: "Slide 24: Streaming Responses"
slide_number: 24
tags: [lecture/slide, langgraph, agentic-programming]
source: https://github.com/trieu/ai-trip-planner/blob/main/docs/AGENTIC_PROGRAMMING_LECTURE.md
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
