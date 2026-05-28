---
title: "Slide 26: Working with Custom Tools"
slide_number: 26
tags: [lecture/slide, langgraph, agentic-programming]
source: https://github.com/trieu/ai-trip-planner/blob/main/docs/AGENTIC_PROGRAMMING_LECTURE.md
---

# Slide 26: Working with Custom Tools

### Tool Anatomy

```python
from langchain.tools import tool
from pydantic import BaseModel, Field

class WeatherInput(BaseModel):
    location: str = Field(description="City name")
    units: str = Field(default="celsius", description="celsius or fahrenheit")

@tool
def get_weather(location: str, units: str = "celsius") -> str:
    """
    Get current weather for a location.
    
    Args:
        location: City name (e.g., "Tokyo")
        units: Temperature units (celsius or fahrenheit)
    
    Returns:
        Formatted weather string
    """
    api_response = call_weather_api(location, units)
    return f"Weather in {location}: {api_response['temp']}°{units[0].upper()}, {api_response['description']}"
```

### Registering Tools with LLM

```python
tools = [
    get_weather,
    get_destination_info,
    get_costs,
]

# With OpenAI
from langchain_openai import ChatOpenAI

llm = ChatOpenAI(model="gpt-4o")
llm_with_tools = llm.bind_tools(tools)
```

---

← [[Slide 25 - Deployment Patterns|Previous]] · [[Index|🏠 Index]] · [[Slide 27 - Tool Calling in Nodes|Next]] →
