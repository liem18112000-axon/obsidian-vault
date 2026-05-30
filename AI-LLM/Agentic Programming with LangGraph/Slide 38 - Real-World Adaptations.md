---
ai_hash: 93ad449bc05d087f
ai_model: google/gemini-2.5-flash
ai_updated: '2026-05-30'
entities:
- Slide 38
- Real-World Adaptations
- PR Description Generator
- trip planner
- GitHub PR descriptions
- research_node
- code_analyzer
- diff
- weather_node
- impact_analyzer
- affected services
- budget_node
- risk_analyzer
- potential issues
- journey_plan_node
- description_generator
- PR description
- TripState
- PRState
- destination
- repo_name
- budget
- weather
- risk_level
- final
- pr_description
- get_destination_info
- get_code_diff
- get_costs
- get_risk_assessment
- get_weather
- get_related_tests
- Customer Support Classifier
- knowledge_base_search
- sentiment_analyzer
- priority_calculator
- response_generator
- ticket
- priority
- KB articles
- sentiment
- escalation risk
- response template
- Slide 37
- Monitoring & Alerting
- Index
- Slide 39
- Future of Agentic Programming
- Agentic Programming
- journey_plan
slide_number: 38
source: https://github.com/trieu/ai-trip-planner/blob/main/docs/AGENTIC_PROGRAMMING_LECTURE.md
tags:
- lecture/slide
- langgraph
- agentic-programming
title: 'Slide 38: Real-World Adaptations'
---

# Slide 38: Real-World Adaptations

### Use Case: PR Description Generator

Adapt the trip planner to auto-generate GitHub PR descriptions:

```python
# Replace nodes
research_node     → code_analyzer (analyze diff)
weather_node      → impact_analyzer (check affected services)
budget_node       → risk_analyzer (potential issues)
journey_plan_node → description_generator (create PR description)

# State changes
TripState → PRState
  destination → repo_name
  budget → affected_services
  weather → risk_level
  final → pr_description

# Tools
get_destination_info → get_code_diff
get_costs → get_risk_assessment
get_weather → get_related_tests
```

### Use Case: Customer Support Classifier

```python
# Nodes
research_node → knowledge_base_search
weather_node  → sentiment_analyzer
budget_node   → priority_calculator
journey_plan  → response_generator

# Execution
Classify ticket (low/med/high priority)
  ↓
Fetch relevant KB articles
  ↓
Determine sentiment & escalation risk
  ↓
Generate response template
```

---

← [[Slide 37 - Monitoring & Alerting|Previous]] · [[Index|🏠 Index]] · [[Slide 39 - Future of Agentic Programming|Next]] →

%% ai-graph-start %%

**Related notes:**
- [[Slide 3 - The AI Trip Planner - Your Real-World Blueprint]]
- [[Slide 34 - Building Adaptive Agents]]
- [[Index]]
- [[Slide 40 - Recap & Your Turn]]
- [[Slide 39 - Future of Agentic Programming]]

**Relations:**
- Slide 38 — *covers* — Real-World Adaptations
- Real-World Adaptations — *includes use case* — PR Description Generator
- PR Description Generator — *adapts* — trip planner
- PR Description Generator — *generates* — GitHub PR descriptions
- research_node — *is replaced by* — code_analyzer
- code_analyzer — *analyzes* — diff
- weather_node — *is replaced by* — impact_analyzer
- impact_analyzer — *checks* — affected services
- budget_node — *is replaced by* — risk_analyzer
- risk_analyzer — *identifies* — potential issues
- journey_plan_node — *is replaced by* — description_generator
- description_generator — *creates* — PR description
- TripState — *maps to* — PRState
- destination — *maps to* — repo_name
- budget — *maps to* — affected services
- weather — *maps to* — risk_level
- final — *maps to* — pr_description
- get_destination_info — *maps to* — get_code_diff
- get_costs — *maps to* — get_risk_assessment
- get_weather — *maps to* — get_related_tests
- Real-World Adaptations — *includes use case* — Customer Support Classifier
- research_node — *maps to* — knowledge_base_search
- weather_node — *maps to* — sentiment_analyzer
- budget_node — *maps to* — priority_calculator
- journey_plan — *maps to* — response_generator
- Customer Support Classifier — *classifies* — ticket
- ticket — *has* — priority
- knowledge_base_search — *fetches* — KB articles
- sentiment_analyzer — *determines* — sentiment
- sentiment_analyzer — *determines* — escalation risk
- response_generator — *generates* — response template
- Slide 38 — *follows* — Slide 37
- Slide 37 — *is titled* — Monitoring & Alerting
- Slide 38 — *precedes* — Slide 39
- Slide 39 — *is titled* — Future of Agentic Programming
- Future of Agentic Programming — *discusses* — Agentic Programming
- Slide 38 — *links to* — Index

%% ai-graph-end %%