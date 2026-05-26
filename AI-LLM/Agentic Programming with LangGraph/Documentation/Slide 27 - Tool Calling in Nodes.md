---
title: "Slide 27: Tool Calling in Nodes"
slide_number: 27
tags: [lecture/slide, langgraph, agentic-programming]
source: https://github.com/trieu/ai-trip-planner/blob/main/docs/AGENTIC_PROGRAMMING_LECTURE.md
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
