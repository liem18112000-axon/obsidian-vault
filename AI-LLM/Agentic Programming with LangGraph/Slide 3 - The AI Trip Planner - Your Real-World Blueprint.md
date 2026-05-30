---
ai_hash: f376e16f1d322e47
ai_model: google/gemini-2.5-flash
ai_updated: '2026-05-30'
entities:
- Slide 3
- AI Trip Planner
- Real-World Blueprint
- Problem
- Personalized Trip
- Authentic Local Experiences
- Budget Constraints
- Weather Conditions
- User Preferences
- Traditional Approach
- Single LLM
- Agentic Approach
- Parallel Agents
- Research Agent
- Budget Agent
- Weather Agent
- Aggregator
- Journey Plan
- Better Decisions
- Cited Sources
- Multi-Source Data Synthesis
- Slide 2 - What is Agentic Programming
- Index
- Slide 4 - LangGraph Core Concepts
slide_number: 3
source: https://github.com/trieu/ai-trip-planner/blob/main/docs/AGENTIC_PROGRAMMING_LECTURE.md
tags:
- lecture/slide
- langgraph
- agentic-programming
title: 'Slide 3: The AI Trip Planner: Your Real-World Blueprint'
---

# Slide 3: The AI Trip Planner: Your Real-World Blueprint

### The Problem

Plan a personalized trip that balances:
- 🏖️ Authentic local experiences (research)
- 💰 Budget constraints (budget planning)
- 🌤️ Weather conditions (weather data)
- 👤 User preferences (personalization)

### Traditional Approach: Single LLM

```python
# ❌ Old way - single call, low quality
response = llm.invoke(f"Plan a {budget} trip to {destination}")
```

### Agentic Approach: Parallel Agents

```
Research Agent ──┐
Budget Agent ────→ Aggregator → Journey Plan
Weather Agent ───┘
```

🚀 **Result:** Better decisions, cited sources, multi-source data synthesis

---

← [[Slide 2 - What is Agentic Programming|Previous]] · [[Index|🏠 Index]] · [[Slide 4 - LangGraph Core Concepts|Next]] →

%% ai-graph-start %%

**Related notes:**
- [[Slide 1 - Course Overview]]
- [[Slide 2 - What is Agentic Programming]]
- [[Slide 15 - The Journey Plan Node (LLM Synthesis)]]
- [[Slide 40 - Recap & Your Turn]]
- [[Index]]

**Relations:**
- Slide 3 — *introduces* — AI Trip Planner
- AI Trip Planner — *is a* — Real-World Blueprint
- AI Trip Planner — *addresses* — Problem
- Problem — *is to plan* — Personalized Trip
- Personalized Trip — *balances* — Authentic Local Experiences
- Personalized Trip — *balances* — Budget Constraints
- Personalized Trip — *balances* — Weather Conditions
- Personalized Trip — *balances* — User Preferences
- Traditional Approach — *uses* — Single LLM
- Agentic Approach — *uses* — Parallel Agents
- Parallel Agents — *includes* — Research Agent
- Parallel Agents — *includes* — Budget Agent
- Parallel Agents — *includes* — Weather Agent
- Research Agent — *feeds into* — Aggregator
- Budget Agent — *feeds into* — Aggregator
- Weather Agent — *feeds into* — Aggregator
- Aggregator — *produces* — Journey Plan
- Agentic Approach — *results in* — Better Decisions
- Agentic Approach — *results in* — Cited Sources
- Agentic Approach — *results in* — Multi-Source Data Synthesis
- Slide 3 — *is previous to* — Slide 2 - What is Agentic Programming
- Slide 3 — *is part of* — Index
- Slide 3 — *is next to* — Slide 4 - LangGraph Core Concepts

%% ai-graph-end %%