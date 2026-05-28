---
title: "Slide 39: Future of Agentic Programming"
slide_number: 39
tags: [lecture/slide, langgraph, agentic-programming]
source: https://github.com/trieu/ai-trip-planner/blob/main/docs/AGENTIC_PROGRAMMING_LECTURE.md
---

# Slide 39: Future of Agentic Programming

### Emerging Patterns

1. **Multi-Turn Conversations** - Agents that remember context across turns
2. **Hierarchical Agents** - Agents that manage sub-agents
3. **Tool Learning** - Agents that learn which tools work best
4. **Reflection** - Agents that critique their own outputs
5. **Human-in-the-Loop** - Agents that ask for clarification

### Example: Reflection Loop

```python
def _reflection_node(state):
    """Agent critiques its own output."""
    itinerary = state.get("final", "")
    
    critique = llm.invoke(f"""
        Review this itinerary for issues:
        {itinerary}
        
        Check for:
        1. Infeasible timing (too much travel)?
        2. Budget overruns?
        3. Weather conflicts?
        
        Return: GOOD or NEEDS_IMPROVEMENT
    """)
    
    if "NEEDS_IMPROVEMENT" in critique.content:
        return {"needs_revision": True}
    else:
        return {"needs_revision": False}

builder.add_conditional_edges(
    "journey_plan",
    lambda state: "refine" if state.get("needs_revision") else "end",
    {"refine": "refine_node", "end": END}
)
```

---

← [[Slide 38 - Real-World Adaptations|Previous]] · [[Index|🏠 Index]] · [[Slide 40 - Recap & Your Turn|Next]] →
