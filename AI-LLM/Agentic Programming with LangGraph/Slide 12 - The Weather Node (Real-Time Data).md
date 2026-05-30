---
ai_hash: b95fa211fb896d35
ai_model: google/gemini-2.5-flash
ai_updated: '2026-05-30'
entities:
- Slide 12
- The Weather Node
- Real-Time Data
- _weather_node
- TripState
- destination
- get_current_weather
- location
- unit
- Open-Meteo
- Graceful Fallback
- System
- Downstream agents
- Slide 11
- The Research Node
- Tool Integration
- Index
- Slide 13
- The Budget Node
- Business Logic
- tool_calls
- weather
slide_number: 12
source: https://github.com/trieu/ai-trip-planner/blob/main/docs/AGENTIC_PROGRAMMING_LECTURE.md
tags:
- lecture/slide
- langgraph
- agentic-programming
title: 'Slide 12: The Weather Node (Real-Time Data)'
---

# Slide 12: The Weather Node (Real-Time Data)

### Simple Tool Integration

```python
def _weather_node(self, state: TripState) -> Dict[str, Any]:
    dest = state.get("trip_request", {}).get("destination")
    
    if not dest or not isinstance(dest, str):
        return {
            "weather": "Invalid destination",
            "tool_calls": []
        }
    
    try:
        # Call tool (synchronous here)
        weather = get_current_weather.invoke({"location": dest.strip()})
    
    except Exception as e:
        logger.error(f"[weather_node] failed: {e}")
        weather = "Weather unavailable (fallback to planning without it)"
    
    return {
        "weather": weather,
        "tool_calls": [{
            "tool": "get_current_weather",
            "args": {"location": dest}
        }]
    }
```

### Tool Definition

```python
@tool
def get_current_weather(location: str, unit: str = "celsius") -> str:
    """Get current weather for a location."""
    # Current repo flow:
    # 1. Resolve coordinates
    # 2. Call Open-Meteo
    # 3. Return a short weather summary
    weather_data = fetch_open_meteo(location, unit)
    return f"Weather in {location}: {weather_data['temp']}°C"
```

### Key Pattern: Graceful Fallback

If tool fails → return sensible default
→ System continues (doesn't crash)
→ Downstream agents adapt

---

← [[Slide 11 - The Research Node (Tool Integration)|Previous]] · [[Index|🏠 Index]] · [[Slide 13 - The Budget Node (Business Logic)|Next]] →

%% ai-graph-start %%

**Related notes:**
- [[Slide 27 - Tool Calling in Nodes]]
- [[Slide 26 - Working with Custom Tools]]
- [[Slide 13 - The Budget Node (Business Logic)]]
- [[Slide 11 - The Research Node (Tool Integration)]]
- [[Slide 18 - Error Handling in Agentic Systems]]

**Relations:**
- Slide 12 — *is titled* — The Weather Node (Real-Time Data)
- The Weather Node — *processes* — Real-Time Data
- _weather_node — *is a component of* — The Weather Node
- _weather_node — *receives* — TripState
- TripState — *contains* — destination
- _weather_node — *invokes* — get_current_weather
- get_current_weather — *requires* — location
- get_current_weather — *can specify* — unit
- get_current_weather — *calls* — Open-Meteo
- The Weather Node — *demonstrates* — Graceful Fallback
- Graceful Fallback — *ensures* — System continues
- Graceful Fallback — *allows* — Downstream agents adapt
- Slide 12 — *follows* — Slide 11
- Slide 11 — *is titled* — The Research Node (Tool Integration)
- Slide 12 — *precedes* — Slide 13
- Slide 13 — *is titled* — The Budget Node (Business Logic)
- Slide 12 — *is linked to* — Index
- _weather_node — *returns* — weather
- _weather_node — *returns* — tool_calls
- tool_calls — *references* — get_current_weather
- get_current_weather — *provides* — weather summary

%% ai-graph-end %%