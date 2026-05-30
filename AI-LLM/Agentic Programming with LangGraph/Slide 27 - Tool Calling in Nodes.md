---
ai_hash: b7ade218a5d497f0
ai_model: google/gemini-2.5-flash
ai_updated: '2026-05-30'
entities:
- Slide 27
- Tool Calling in Nodes
- invoke()
- ainvoke()
- LLM Tool Calling (Agent Loop)
- get_weather
- get_destination_info
- TripState
- SystemMessage
- HumanMessage
- LLM
- get_tool_by_name
- Slide 26
- Index
- Slide 28
- _weather_node
- _research_node
- _research_node_llm_driven
slide_number: 27
source: https://github.com/trieu/ai-trip-planner/blob/main/docs/AGENTIC_PROGRAMMING_LECTURE.md
tags:
- lecture/slide
- langgraph
- agentic-programming
title: 'Slide 27: Tool Calling in Nodes'
---

# Slide 27: Tool Calling in Nodes

### Method 1: invoke() - Synchronous

```python
def _weather_node(self, state: TripState) -> Dict[str, Any]:
    result = get_weather.invoke({"location": "Tokyo"})
    return {"weather": result}
```

### Method 2: ainvoke() - Asynchronous

```python
async def _research_node(self, state: TripState) -> Dict[str, Any]:
    result = await get_destination_info.ainvoke({"destination": "Tokyo"})
    return {"research": result}
```

### Method 3: LLM Tool Calling (Agent Loop)

```python
async def _research_node_llm_driven(self, state: TripState) -> Dict[str, Any]:
    """Let LLM decide which tools to call."""
    messages = [
        SystemMessage("You are a travel researcher. Use tools to research destinations."),
        HumanMessage(f"Research Tokyo for a 7-day trip")
    ]
    
    # Bind tools to LLM
    llm_with_tools = self.llm.bind_tools([get_destination_info, get_weather])
    
    # LLM decides which tools to call
    response = await llm_with_tools.ainvoke(messages)
    
    # Check if tool calls were requested
    if response.tool_calls:
        # Execute tools
        results = []
        for tool_call in response.tool_calls:
            tool = get_tool_by_name(tool_call["name"])
            result = tool.invoke(tool_call["args"])
            results.append(result)
        
        return {"research": " ".join(results)}
    else:
        return {"research": response.content}
```

---

← [[Slide 26 - Working with Custom Tools|Previous]] · [[Index|🏠 Index]] · [[Slide 28 - Prompt Engineering for Agentic Systems|Next]] →

%% ai-graph-start %%

**Related notes:**
- [[Slide 26 - Working with Custom Tools]]
- [[Slide 16 - Running the Graph]]
- [[Slide 12 - The Weather Node (Real-Time Data)]]
- [[Slide 36 - Tool Composition]]
- [[Slide 1 - Course Overview]]

**Relations:**
- Slide 27 — *covers* — Tool Calling in Nodes
- Tool Calling in Nodes — *describes method* — invoke()
- Tool Calling in Nodes — *describes method* — ainvoke()
- Tool Calling in Nodes — *describes method* — LLM Tool Calling (Agent Loop)
- invoke() — *is* — Synchronous
- ainvoke() — *is* — Asynchronous
- _weather_node — *implements* — invoke()
- _weather_node — *calls tool* — get_weather
- _weather_node — *takes parameter* — TripState
- _research_node — *implements* — ainvoke()
- _research_node — *calls tool* — get_destination_info
- _research_node — *takes parameter* — TripState
- _research_node_llm_driven — *implements* — LLM Tool Calling (Agent Loop)
- _research_node_llm_driven — *uses* — SystemMessage
- _research_node_llm_driven — *uses* — HumanMessage
- _research_node_llm_driven — *uses* — LLM
- _research_node_llm_driven — *uses* — get_tool_by_name
- _research_node_llm_driven — *takes parameter* — TripState
- LLM — *binds tool* — get_destination_info
- LLM — *binds tool* — get_weather
- Slide 27 — *follows* — Slide 26
- Slide 27 — *precedes* — Slide 28
- Slide 27 — *links to* — Index

%% ai-graph-end %%