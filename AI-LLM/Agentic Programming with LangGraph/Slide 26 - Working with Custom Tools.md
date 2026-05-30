---
ai_hash: 0ea35dd9fd554752
ai_model: google/gemini-2.5-flash
ai_updated: '2026-05-30'
entities:
- Slide 26
- Custom Tools
- Tool Anatomy
- '@tool'
- langchain.tools
- pydantic
- BaseModel
- Field
- WeatherInput
- get_weather
- call_weather_api
- Registering Tools with LLM
- tools
- get_destination_info
- get_costs
- OpenAI
- langchain_openai
- ChatOpenAI
- llm
- gpt-4o
- llm_with_tools
- Slide 25
- Deployment Patterns
- Index
- Slide 27
- Tool Calling in Nodes
slide_number: 26
source: https://github.com/trieu/ai-trip-planner/blob/main/docs/AGENTIC_PROGRAMMING_LECTURE.md
tags:
- lecture/slide
- langgraph
- agentic-programming
title: 'Slide 26: Working with Custom Tools'
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

%% ai-graph-start %%

**Related notes:**
- [[Slide 27 - Tool Calling in Nodes]]
- [[Slide 36 - Tool Composition]]
- [[Slide 12 - The Weather Node (Real-Time Data)]]
- [[Slide 11 - The Research Node (Tool Integration)]]
- [[Slide 28 - Prompt Engineering for Agentic Systems]]

**Relations:**
- Slide 26 — *is_about* — Custom Tools
- Slide 26 — *has_part* — Tool Anatomy
- Slide 26 — *has_part* — Registering Tools with LLM
- Tool Anatomy — *describes* — Custom Tools
- langchain.tools — *provides* — @tool
- pydantic — *provides* — BaseModel
- pydantic — *provides* — Field
- WeatherInput — *inherits_from* — BaseModel
- WeatherInput — *uses* — Field
- get_weather — *is_an_example_of* — Custom Tools
- get_weather — *is_decorated_by* — @tool
- get_weather — *has_input_schema* — WeatherInput
- get_weather — *calls* — call_weather_api
- tools — *contains* — get_weather
- tools — *contains* — get_destination_info
- tools — *contains* — get_costs
- ChatOpenAI — *is_from_library* — langchain_openai
- llm — *is_an_instance_of* — ChatOpenAI
- llm — *configures_with_model* — gpt-4o
- llm_with_tools — *binds* — tools
- llm_with_tools — *to* — llm
- Slide 26 — *follows* — Slide 25
- Slide 25 — *is_titled* — Deployment Patterns
- Slide 26 — *precedes* — Slide 27
- Slide 27 — *is_titled* — Tool Calling in Nodes
- Index — *is_a* — Navigation Link

%% ai-graph-end %%