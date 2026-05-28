---
title: "Slide 3: The AI Trip Planner: Your Real-World Blueprint"
slide_number: 3
tags: [lecture/slide, langgraph, agentic-programming]
source: https://github.com/trieu/ai-trip-planner/blob/main/docs/AGENTIC_PROGRAMMING_LECTURE.md
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
