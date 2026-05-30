---
ai_hash: 79b66624c3247cb8
ai_model: google/gemini-2.5-flash
ai_updated: '2026-05-30'
entities:
- Slide 31
- Scaling
- Performance Optimization
- Latency Bottlenecks
- TripPlanner Execution Timeline
- Sequential Execution
- Parallel Execution
- LangGraph
- LLM calls
- Further Optimization
- Cache LLM responses
- RedisCache
- Redis
- Parallel LLM calls
- asyncio.gather
- get_destination_info
- web_search
- rag_service.retrieve
- merge_results
- Smaller models
- Fast paths
- ChatGoogleGenerativeAI
- gemini-2.5-flash-lite
- ChatOpenAI
- gpt-4o
- Slide 30 - RAG (Retrieval-Augmented Generation)
- Index
- Slide 32 - Common Pitfalls & How to Avoid Them
slide_number: 31
source: https://github.com/trieu/ai-trip-planner/blob/main/docs/AGENTIC_PROGRAMMING_LECTURE.md
tags:
- lecture/slide
- langgraph
- agentic-programming
title: 'Slide 31: Scaling & Performance Optimization'
---

# Slide 31: Scaling & Performance Optimization

### Latency Bottlenecks

```
TripPlanner Execution Timeline:
┌─────────────────────────────────────────────────┐
│ Sequential (Old):                               │
│ ├── research: 2000ms                            │
│ ├── weather: 100ms                              │
│ ├── budget: 150ms                               │
│ ├── aggregate: 10ms                             │
│ └── journey_plan: 3000ms                        │
│ TOTAL: 5260ms ❌                                │
└─────────────────────────────────────────────────┘

┌──────────────────────────────────────────────┐
│ Parallel (LangGraph):                         │
│ ├── research: 2000ms   ┐                      │
│ ├── weather: 100ms     ├─ All simultaneous   │
│ ├── budget: 150ms      ┘                      │
│ ├── aggregate: 10ms                          │
│ └── journey_plan: 3000ms                     │
│ TOTAL: 5160ms (saved 100ms) ⚠️              │
│ Real gain would be if LLM calls were async  │
└──────────────────────────────────────────────┘
```

### Further Optimization

```python
# 1. Cache LLM responses
from langchain.globals import set_llm_cache
from langchain_redis import RedisCache

set_llm_cache(RedisCache(redis_url="redis://localhost:6379"))

# 2. Parallel LLM calls
async def _optimized_research_node(self, state):
    dest = state["trip_request"]["destination"]
    interests = state["user_profile"]["current_interests"]
    
    # Fetch multiple sources in parallel
    results = await asyncio.gather(
        get_destination_info.ainvoke({"destination": dest}),
        web_search.ainvoke({"query": f"hidden gems {dest}"}),
        rag_service.retrieve.ainvoke({"destination": dest, "interests": interests})
    )
    
    return {"research": merge_results(results)}

# 3. Use smaller models for fast paths
lightweight_llm = ChatGoogleGenerativeAI(model="gemini-2.5-flash-lite")
heavyweight_llm = ChatOpenAI(model="gpt-4o")
```

---

← [[Slide 30 - RAG (Retrieval-Augmented Generation)|Previous]] · [[Index|🏠 Index]] · [[Slide 32 - Common Pitfalls & How to Avoid Them|Next]] →

%% ai-graph-start %%

**Related notes:**
- [[Slide 30 - RAG (Retrieval-Augmented Generation)]]
- [[Slide 5 - The Trip Planner Architecture]]
- [[Slide 40 - Recap & Your Turn]]
- [[Slide 3 - The AI Trip Planner - Your Real-World Blueprint]]
- [[Slide 34 - Building Adaptive Agents]]

**Relations:**
- Slide 31 — *covers* — Scaling
- Slide 31 — *covers* — Performance Optimization
- Performance Optimization — *addresses* — Latency Bottlenecks
- Latency Bottlenecks — *illustrated by* — TripPlanner Execution Timeline
- TripPlanner Execution Timeline — *compares* — Sequential Execution
- TripPlanner Execution Timeline — *compares* — Parallel Execution
- Parallel Execution — *uses* — LangGraph
- Parallel Execution — *benefits from* — async LLM calls
- Performance Optimization — *includes* — Further Optimization
- Further Optimization — *includes* — Cache LLM responses
- Cache LLM responses — *uses* — RedisCache
- RedisCache — *integrates with* — Redis
- Further Optimization — *includes* — Parallel LLM calls
- Parallel LLM calls — *uses* — asyncio.gather
- Parallel LLM calls — *involves* — get_destination_info
- Parallel LLM calls — *involves* — web_search
- Parallel LLM calls — *involves* — rag_service.retrieve
- Parallel LLM calls — *uses* — merge_results
- Further Optimization — *includes* — Smaller models for Fast paths
- Smaller models — *exemplified by* — ChatGoogleGenerativeAI
- ChatGoogleGenerativeAI — *uses model* — gemini-2.5-flash-lite
- Heavyweight LLM — *exemplified by* — ChatOpenAI
- ChatOpenAI — *uses model* — gpt-4o
- Slide 31 — *follows* — Slide 30 - RAG (Retrieval-Augmented Generation)
- Slide 31 — *precedes* — Slide 32 - Common Pitfalls & How to Avoid Them
- Slide 31 — *links to* — Index

%% ai-graph-end %%