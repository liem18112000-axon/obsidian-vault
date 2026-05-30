---
ai_hash: 59ee704065e36d1c
ai_model: google/gemini-2.5-flash
ai_updated: '2026-05-30'
entities:
- Slide 36
- Tool Composition
- Simple Tool
- Composite Tool
- get_weather
- fetch_weather_api
- smart_destination_research
- RAG
- web search
- LLM
- rag_service
- web_search_api
- llm (instance)
- HumanMessage
- Tool + LLM
- Slide 35
- Agent Chains
- Agent Trees
- Index
- Slide 37
- Monitoring & Alerting
slide_number: 36
source: https://github.com/trieu/ai-trip-planner/blob/main/docs/AGENTIC_PROGRAMMING_LECTURE.md
tags:
- lecture/slide
- langgraph
- agentic-programming
title: 'Slide 36: Tool Composition'
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

%% ai-graph-start %%

**Related notes:**
- [[Slide 27 - Tool Calling in Nodes]]
- [[Slide 26 - Working with Custom Tools]]
- [[Slide 11 - The Research Node (Tool Integration)]]
- [[Slide 30 - RAG (Retrieval-Augmented Generation)]]
- [[Slide 15 - The Journey Plan Node (LLM Synthesis)]]

**Relations:**
- Slide 36 — *covers* — Tool Composition
- Tool Composition — *includes* — Simple Tool
- Tool Composition — *includes* — Composite Tool
- Simple Tool — *example* — get_weather
- get_weather — *uses* — fetch_weather_api
- Composite Tool — *example* — smart_destination_research
- smart_destination_research — *uses* — RAG
- smart_destination_research — *uses* — web search
- smart_destination_research — *uses* — LLM
- smart_destination_research — *uses* — rag_service
- smart_destination_research — *uses* — web_search_api
- smart_destination_research — *invokes* — llm (instance)
- llm (instance) — *processes* — HumanMessage
- Composite Tool — *is_composed_of* — Tool + LLM
- Slide 36 — *follows* — Slide 35
- Slide 35 — *covers* — Agent Chains
- Slide 35 — *covers* — Agent Trees
- Slide 36 — *precedes* — Slide 37
- Slide 37 — *covers* — Monitoring & Alerting
- Slide 36 — *is_part_of* — Index

%% ai-graph-end %%