---
title: "Slide 31: Scaling & Performance Optimization"
slide_number: 31
tags: [lecture/slide, langgraph, agentic-programming]
source: https://github.com/trieu/ai-trip-planner/blob/main/docs/AGENTIC_PROGRAMMING_LECTURE.md
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
