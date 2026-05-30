---
ai_hash: 4db804c37fb7ebc2
ai_model: google/gemini-2.5-flash
ai_updated: '2026-05-30'
entities:
- Slide 39
- Future of Agentic Programming
- Agentic Programming
- Emerging Patterns
- Multi-Turn Conversations
- Agents
- Hierarchical Agents
- sub-agents
- Tool Learning
- Reflection
- Human-in-the-Loop
- Reflection Loop
- Python
- _reflection_node
- itinerary
- llm
- critique
- Infeasible timing
- Budget overruns
- Weather conflicts
- builder
- journey_plan
- Slide 38 - Real-World Adaptations
- Index
- Slide 40 - Recap & Your Turn
slide_number: 39
source: https://github.com/trieu/ai-trip-planner/blob/main/docs/AGENTIC_PROGRAMMING_LECTURE.md
tags:
- lecture/slide
- langgraph
- agentic-programming
title: 'Slide 39: Future of Agentic Programming'
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

%% ai-graph-start %%

**Related notes:**
- [[Slide 40 - Recap & Your Turn]]
- [[Slide 3 - The AI Trip Planner - Your Real-World Blueprint]]
- [[Index]]
- [[Slide 2 - What is Agentic Programming]]
- [[Slide 1 - Course Overview]]

**Relations:**
- Slide 39 — *is titled* — Future of Agentic Programming
- Future of Agentic Programming — *discusses* — Emerging Patterns
- Emerging Patterns — *include* — Multi-Turn Conversations
- Multi-Turn Conversations — *involve* — Agents
- Agents — *remember context in* — Multi-Turn Conversations
- Emerging Patterns — *include* — Hierarchical Agents
- Hierarchical Agents — *manage* — sub-agents
- Emerging Patterns — *include* — Tool Learning
- Tool Learning — *involves* — Agents
- Agents — *learn tools in* — Tool Learning
- Emerging Patterns — *include* — Reflection
- Reflection — *involves* — Agents
- Agents — *critique outputs in* — Reflection
- Emerging Patterns — *include* — Human-in-the-Loop
- Human-in-the-Loop — *involves* — Agents
- Agents — *ask for clarification in* — Human-in-the-Loop
- Reflection Loop — *is an example of* — Reflection
- Reflection Loop — *is implemented in* — Python
- _reflection_node — *is part of* — Reflection Loop
- _reflection_node — *is a function in* — Python
- _reflection_node — *critiques* — itinerary
- itinerary — *is a variable in* — _reflection_node
- llm — *invokes* — critique
- critique — *checks for* — Infeasible timing
- critique — *checks for* — Budget overruns
- critique — *checks for* — Weather conflicts
- builder — *adds conditional edges to* — journey_plan
- Slide 39 — *follows* — Slide 38 - Real-World Adaptations
- Slide 39 — *precedes* — Slide 40 - Recap & Your Turn
- Index — *links to* — Slide 39
- Index — *links to* — Slide 38 - Real-World Adaptations
- Index — *links to* — Slide 40 - Recap & Your Turn

%% ai-graph-end %%