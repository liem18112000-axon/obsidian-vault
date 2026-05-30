---
ai_hash: 8c5d0402e4051ea3
ai_model: google/gemini-2.5-flash
ai_updated: '2026-05-30'
entities:
- Slide 34
- Building Adaptive Agents
- Context-Aware Routing
- _route_research_strategy
- TripState
- web_search
- rag_with_llm
- builder
- add_conditional_edges
- load_profile
- web_research_node
- rag_research_node
- Learning from Feedback
- store_feedback
- user_id
- trip_id
- feedback_score
- db
- trip_feedback
- get_user_preferences
- trips
- analyze_patterns
- Slide 33 - Debugging Techniques
- Index
- Slide 35 - Agent Chains vs Agent Trees
slide_number: 34
source: https://github.com/trieu/ai-trip-planner/blob/main/docs/AGENTIC_PROGRAMMING_LECTURE.md
tags:
- lecture/slide
- langgraph
- agentic-programming
title: 'Slide 34: Building Adaptive Agents'
---

# Slide 34: Building Adaptive Agents

### Context-Aware Routing

```python
def _route_research_strategy(state: TripState) -> str:
    """Choose research strategy based on destination."""
    dest = state["trip_request"]["destination"].lower()
    
    # Well-known destinations → web search likely to be better
    if dest in ["tokyo", "paris", "new york", "london"]:
        return "web_search"
    
    # Niche destinations → use RAG + LLM
    else:
        return "rag_with_llm"

builder.add_conditional_edges(
    "load_profile",
    _route_research_strategy,
    {
        "web_search": "web_research_node",
        "rag_with_llm": "rag_research_node"
    }
)
```

### Learning from Feedback

```python
# Store user feedback after each trip
def store_feedback(user_id, trip_id, feedback_score: 1-5):
    db.execute("""
        INSERT INTO trip_feedback (user_id, trip_id, score, created_at)
        VALUES (:user_id, :trip_id, :score, NOW())
    """, {"user_id": user_id, "trip_id": trip_id, "score": feedback_score})

# Use feedback to adapt
def get_user_preferences(user_id):
    # Get high-rated trips
    high_rated = db.query("""
        SELECT trip_details FROM trips
        WHERE user_id = :user_id AND feedback_score >= 4
    """, {"user_id": user_id})
    
    # Extract patterns (prefer luxury? cultural? nature?)
    return analyze_patterns(high_rated)
```

---

← [[Slide 33 - Debugging Techniques|Previous]] · [[Index|🏠 Index]] · [[Slide 35 - Agent Chains vs Agent Trees|Next]] →

%% ai-graph-start %%

**Related notes:**
- [[Slide 19 - Conditional Branching (Advanced)]]
- [[Slide 18 - Error Handling in Agentic Systems]]
- [[Slide 30 - RAG (Retrieval-Augmented Generation)]]
- [[Slide 28 - Prompt Engineering for Agentic Systems]]
- [[Index]]

**Relations:**
- Slide 34 — *discusses* — Building Adaptive Agents
- Building Adaptive Agents — *includes concept* — Context-Aware Routing
- Building Adaptive Agents — *includes concept* — Learning from Feedback
- Context-Aware Routing — *implemented by function* — _route_research_strategy
- _route_research_strategy — *takes input* — TripState
- _route_research_strategy — *returns strategy* — web_search
- _route_research_strategy — *returns strategy* — rag_with_llm
- builder — *uses method* — add_conditional_edges
- add_conditional_edges — *uses function* — _route_research_strategy
- add_conditional_edges — *starts from* — load_profile
- add_conditional_edges — *routes web_search to* — web_research_node
- add_conditional_edges — *routes rag_with_llm to* — rag_research_node
- Learning from Feedback — *implemented by function* — store_feedback
- Learning from Feedback — *implemented by function* — get_user_preferences
- store_feedback — *stores data* — user_id
- store_feedback — *stores data* — trip_id
- store_feedback — *stores data* — feedback_score
- store_feedback — *interacts with* — db
- db — *contains table* — trip_feedback
- get_user_preferences — *uses data* — user_id
- get_user_preferences — *queries* — db
- db — *contains table* — trips
- get_user_preferences — *uses function* — analyze_patterns
- Slide 34 — *follows* — Slide 33 - Debugging Techniques
- Slide 34 — *precedes* — Slide 35 - Agent Chains vs Agent Trees
- Slide 34 — *links to* — Index

%% ai-graph-end %%