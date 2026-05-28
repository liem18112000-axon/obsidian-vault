---
title: "Slide 34: Building Adaptive Agents"
slide_number: 34
tags: [lecture/slide, langgraph, agentic-programming]
source: https://github.com/trieu/ai-trip-planner/blob/main/docs/AGENTIC_PROGRAMMING_LECTURE.md
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
