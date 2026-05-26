---
title: "Slide 35: Agent Chains vs Agent Trees"
slide_number: 35
tags: [lecture/slide, langgraph, agentic-programming]
source: https://github.com/trieu/ai-trip-planner/blob/main/docs/AGENTIC_PROGRAMMING_LECTURE.md
---

# Slide 35: Agent Chains vs Agent Trees

### Agent Chain (Sequential)

```
Agent1 → Agent2 → Agent3 → Output

Use when:
- Each agent refines previous output
- Sequential reasoning needed (step A → step B → step C)
- Example: Draft → Review → Polish
```

```python
builder.add_edge("draft_agent", "review_agent")
builder.add_edge("review_agent", "polish_agent")
builder.add_edge("polish_agent", END)
```

### Agent Tree (Parallel Branches)

```
Agent1 ─┐
Agent2  ├→ Synthesizer → Output
Agent3 ─┘

Use when:
- Agents solve independent aspects
- Results must be combined
- Example: Research + Budget + Weather (like our trip planner!)
```

```python
builder.add_edge("load_profile", "research")
builder.add_edge("load_profile", "budget")
builder.add_edge("load_profile", "weather")

builder.add_edge("research", "synthesizer")
builder.add_edge("budget", "synthesizer")
builder.add_edge("weather", "synthesizer")
```

---

← [[Slide 34 - Building Adaptive Agents|Previous]] · [[Index|🏠 Index]] · [[Slide 36 - Tool Composition|Next]] →
