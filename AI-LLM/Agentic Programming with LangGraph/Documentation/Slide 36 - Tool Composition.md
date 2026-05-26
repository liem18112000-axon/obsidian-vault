---
title: "Slide 36: Tool Composition"
slide_number: 36
tags: [lecture/slide, langgraph, agentic-programming]
source: https://github.com/trieu/ai-trip-planner/blob/main/docs/AGENTIC_PROGRAMMING_LECTURE.md
---

# Slide 36: Tool Composition

### Simple Tool

```python
@tool
def get_weather(location: str) -> str:
    """Get weather for a location."""
    return fetch_weather_api(location)
```

### Composite Tool (Tool + LLM)

```python
@tool
async def smart_destination_research(destination: str, interests: list) -> str:
    """
    Research a destination intelligently.
    
    Uses RAG, web search, and LLM reasoning to provide comprehensive insights.
    """
    # Step 1: Try RAG
    rag_results = await rag_service.search(destination, interests)
    
    # Step 2: Augment with web search
    web_results = await web_search_api(f"{destination} {' '.join(interests)}")
    
    # Step 3: Let LLM synthesize
    synthesis = await llm.ainvoke([
        HumanMessage(f"""
            Combine these sources into a cohesive guide for {destination}:
            
            RAG Results:
            {rag_results}
            
            Web Results:
            {web_results}
            
            Format as a travel guide.
        """)
    ])
    
    return synthesis.content
```

---

← [[Slide 35 - Agent Chains vs Agent Trees|Previous]] · [[Index|🏠 Index]] · [[Slide 37 - Monitoring & Alerting|Next]] →
