---
title: "Slide 12: The Weather Node (Real-Time Data)"
slide_number: 12
tags: [lecture/slide, langgraph, agentic-programming]
source: https://github.com/trieu/ai-trip-planner/blob/main/docs/AGENTIC_PROGRAMMING_LECTURE.md
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
