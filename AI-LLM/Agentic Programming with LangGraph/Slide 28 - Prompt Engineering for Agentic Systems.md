---
ai_hash: 526baba50cfbef72
ai_model: google/gemini-2.5-flash
ai_updated: '2026-05-30'
entities:
- Slide 28
- Prompt Engineering for Agentic Systems
- Prompt Engineering
- Agentic Systems
- RESEARCH_AGENT_PROMPT
- BUDGET_AGENT_PROMPT
- backend/core_llm/prompt_builder.py
- Slide 27 - Tool Calling in Nodes
- Index
- Slide 29 - Meta-LLM (Provider Agnosticism)
slide_number: 28
source: https://github.com/trieu/ai-trip-planner/blob/main/docs/AGENTIC_PROGRAMMING_LECTURE.md
tags:
- lecture/slide
- langgraph
- agentic-programming
title: 'Slide 28: Prompt Engineering for Agentic Systems'
---

# Slide 28: Prompt Engineering for Agentic Systems

### Don't Just Use Defaults

Tailor prompts to agent roles:

```python
# backend/core_llm/prompt_builder.py

RESEARCH_AGENT_PROMPT = """
You are an expert travel researcher specializing in {destination}.

Your job is to provide the BEST LOCAL EXPERIENCES that align with the user's interests.

**User Profile:**
- Interests: {interests}
- Travel Style: {travel_style}
- Budget Level: {budget_level}

**Requirements:**
1. Include both popular attractions AND hidden gems
2. Provide sources or reasoning for each recommendation
3. Consider seasonal factors: {weather_info}
4. Respect dietary/mobility restrictions if any
5. Format as a bulleted list with cost estimates

**Avoid:**
- Tourist traps with bad reviews
- Activities outside the budget range
- Recommendations unrelated to user interests

Now, research {destination}:
"""

BUDGET_AGENT_PROMPT = """
You are a financial planner for international trips.

Create a detailed budget breakdown for a {duration} trip to {destination}.

**Constraints:**
- Total Budget: {budget}
- Currency: {currency}
- Travel Style: {travel_style}

**Include:**
1. Accommodation (with options: budget, mid-range, luxury)
2. Food (daily breakdown)
3. Activities (aligned with research recommendations)
4. Transportation (local + to/from airport)
5. Contingency (10% buffer)

Format as a table with USD, local currency, and percentage breakdowns.
"""
```

---

← [[Slide 27 - Tool Calling in Nodes|Previous]] · [[Index|🏠 Index]] · [[Slide 29 - Meta-LLM (Provider Agnosticism)|Next]] →

%% ai-graph-start %%

**Related notes:**
- [[Slide 15 - The Journey Plan Node (LLM Synthesis)]]
- [[Slide 3 - The AI Trip Planner - Your Real-World Blueprint]]
- [[Slide 27 - Tool Calling in Nodes]]
- [[Slide 34 - Building Adaptive Agents]]
- [[Slide 13 - The Budget Node (Business Logic)]]

**Relations:**
- Slide 28 — *covers topic* — Prompt Engineering for Agentic Systems
- Prompt Engineering for Agentic Systems — *is a type of* — Prompt Engineering
- Prompt Engineering for Agentic Systems — *applies to* — Agentic Systems
- RESEARCH_AGENT_PROMPT — *is an example of* — Prompt Engineering
- BUDGET_AGENT_PROMPT — *is an example of* — Prompt Engineering
- RESEARCH_AGENT_PROMPT — *is defined in file* — backend/core_llm/prompt_builder.py
- BUDGET_AGENT_PROMPT — *is defined in file* — backend/core_llm/prompt_builder.py
- Slide 28 — *is preceded by* — Slide 27 - Tool Calling in Nodes
- Slide 28 — *is part of* — Index
- Slide 28 — *is followed by* — Slide 29 - Meta-LLM (Provider Agnosticism)

%% ai-graph-end %%