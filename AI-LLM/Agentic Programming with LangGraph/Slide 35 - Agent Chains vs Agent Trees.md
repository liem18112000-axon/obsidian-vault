---
ai_hash: 89bb1a39cd98bcd5
ai_model: google/gemini-2.5-flash
ai_updated: '2026-05-30'
entities:
- Slide 35
- Agent Chains
- Agent Trees
- Sequential Reasoning
- Parallel Branches
- Output
- Draft
- Review
- Polish
- draft_agent
- review_agent
- polish_agent
- END
- Synthesizer
- Research
- Budget
- Weather
- Trip Planner
- load_profile
- Slide 34
- Building Adaptive Agents
- Slide 36
- Tool Composition
slide_number: 35
source: https://github.com/trieu/ai-trip-planner/blob/main/docs/AGENTIC_PROGRAMMING_LECTURE.md
tags:
- lecture/slide
- langgraph
- agentic-programming
title: 'Slide 35: Agent Chains vs Agent Trees'
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

%% ai-graph-start %%

**Related notes:**
- [[Slide 19 - Conditional Branching (Advanced)]]
- [[Index]]
- [[Slide 3 - The AI Trip Planner - Your Real-World Blueprint]]
- [[Slide 8 - Defining Edges (Control Flow)]]
- [[Slide 34 - Building Adaptive Agents]]

**Relations:**
- Slide 35 — *compares* — Agent Chains
- Slide 35 — *compares* — Agent Trees
- Agent Chains — *is a type of* — Sequential Reasoning
- Agent Chains — *refines* — previous output
- Agent Chains — *requires* — Sequential Reasoning
- Agent Chains — *example* — Draft
- Draft — *precedes* — Review
- Review — *precedes* — Polish
- Polish — *produces* — Output
- draft_agent — *connects to* — review_agent
- review_agent — *connects to* — polish_agent
- polish_agent — *connects to* — END
- Agent Trees — *utilizes* — Parallel Branches
- Agent Trees — *solves* — independent aspects
- Agent Trees — *combines* — results
- Agent Trees — *example* — Research
- Agent Trees — *example* — Budget
- Agent Trees — *example* — Weather
- Trip Planner — *uses* — Research
- Trip Planner — *uses* — Budget
- Trip Planner — *uses* — Weather
- load_profile — *connects to* — Research
- load_profile — *connects to* — Budget
- load_profile — *connects to* — Weather
- Research — *connects to* — Synthesizer
- Budget — *connects to* — Synthesizer
- Weather — *connects to* — Synthesizer
- Synthesizer — *produces* — Output
- Slide 35 — *follows* — Slide 34
- Slide 34 — *is titled* — Building Adaptive Agents
- Slide 35 — *precedes* — Slide 36
- Slide 36 — *is titled* — Tool Composition

%% ai-graph-end %%