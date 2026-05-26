---
title: "Slide 38: Real-World Adaptations"
slide_number: 38
tags: [lecture/slide, langgraph, agentic-programming]
source: https://github.com/trieu/ai-trip-planner/blob/main/docs/AGENTIC_PROGRAMMING_LECTURE.md
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
