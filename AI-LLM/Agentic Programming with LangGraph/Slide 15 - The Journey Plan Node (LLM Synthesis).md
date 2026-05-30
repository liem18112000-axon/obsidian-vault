---
ai_hash: 887562842df162b3
ai_model: google/gemini-2.5-flash
ai_updated: '2026-05-30'
entities:
- Slide 15
- The Journey Plan Node
- LLM Synthesis
- Final Reasoning
- Implementation
- _journey_plan_node
- TripState
- build_trip_planner_prompt
- prompt
- llm
- invoke
- SystemMessage
- HumanMessage
- response
- Prompt Template
- trip_request
- destination
- duration
- budget
- user_profile
- current_interests
- weather
- cost_data
- research_data
- travel planner AI
- itinerary
- budget constraint
- user interests
- weather conditions
- hidden gems
- Slide 14 - The Aggregate Node (Data Fusion)
- Index
- Slide 16 - Running the Graph
- research
- profile data
slide_number: 15
source: https://github.com/trieu/ai-trip-planner/blob/main/docs/AGENTIC_PROGRAMMING_LECTURE.md
tags:
- lecture/slide
- langgraph
- agentic-programming
title: 'Slide 15: The Journey Plan Node (LLM Synthesis)'
---

# Slide 15: The Journey Plan Node (LLM Synthesis)

### Final Reasoning

Given all research, weather, budget, and profile data:
→ Synthesize a cohesive, personalized itinerary

### Implementation

```python
def _journey_plan_node(self, state: TripState) -> Dict[str, Any]:
    # Build a rich prompt from the full TripState
    prompt = build_trip_planner_prompt(state)
    
    # Call LLM for final synthesis
    response = self.llm.invoke([
        SystemMessage(content="You are a travel planner."),
        HumanMessage(content=prompt)
    ])
    
    return {
        "final": response.content
    }
```

### Prompt Template (Simplified)

```python
def build_trip_planner_prompt(state: TripState):
    req = state.get("trip_request", {})
    destination = req.get("destination", "Unknown destination")
    duration = req.get("duration", "Unknown duration")
    budget = req.get("budget", "moderate")
    interests = state.get("user_profile", {}).get("current_interests", [])
    weather = state.get("weather", "Not available")
    cost_data = state.get("budget", "Not available")
    research_data = state.get("research", "Not available")

    return f"""
    You are a professional travel planner AI.

    CRITICAL:
    - Output MUST be in the user's preferred language
    - NEVER mix languages

    FORMAT:
    - ONLY use: <h1>, <h2>, <ul>, <li>, <p>, <strong>, <b>, and plain text
    - NO markdown

    INPUT DATA:
    Destination: {destination}
    Duration: {duration}
    Budget: {budget}
    Interests: {interests}
    Weather: {weather}

    COST DATA:
    {cost_data}

    LOCAL RESEARCH:
    {research_data}

    Now create a personalized, day-by-day itinerary that:
    1. Respects the budget constraint
    2. Aligns with user interests
    3. Adapts to weather conditions
    4. Includes hidden gems (not just tourist traps)
    """
```

---

← [[Slide 14 - The Aggregate Node (Data Fusion)|Previous]] · [[Index|🏠 Index]] · [[Slide 16 - Running the Graph|Next]] →

%% ai-graph-start %%

**Related notes:**
- [[Slide 3 - The AI Trip Planner - Your Real-World Blueprint]]
- [[Slide 16 - Running the Graph]]
- [[Appendix A - Full Trip Planner Code Example]]
- [[Slide 28 - Prompt Engineering for Agentic Systems]]
- [[Slide 13 - The Budget Node (Business Logic)]]

**Relations:**
- Slide 15 — *is titled* — The Journey Plan Node (LLM Synthesis)
- The Journey Plan Node — *performs* — LLM Synthesis
- The Journey Plan Node — *has section* — Final Reasoning
- The Journey Plan Node — *has section* — Implementation
- The Journey Plan Node — *has section* — Prompt Template
- Final Reasoning — *synthesizes* — itinerary
- Final Reasoning — *uses data* — research
- Final Reasoning — *uses data* — weather
- Final Reasoning — *uses data* — budget
- Final Reasoning — *uses data* — profile data
- _journey_plan_node — *is part of* — Implementation
- _journey_plan_node — *takes input* — TripState
- _journey_plan_node — *builds* — prompt
- _journey_plan_node — *calls* — llm
- llm — *invokes with* — SystemMessage
- llm — *invokes with* — HumanMessage
- _journey_plan_node — *returns* — response
- build_trip_planner_prompt — *is part of* — Prompt Template
- build_trip_planner_prompt — *takes input* — TripState
- build_trip_planner_prompt — *extracts* — trip_request
- trip_request — *contains* — destination
- trip_request — *contains* — duration
- trip_request — *contains* — budget
- TripState — *contains* — user_profile
- user_profile — *contains* — current_interests
- TripState — *contains* — weather
- TripState — *contains* — cost_data
- TripState — *contains* — research_data
- Prompt Template — *defines role* — travel planner AI
- Prompt Template — *specifies output format* — Output MUST be in the user's preferred language
- Prompt Template — *specifies output format* — NEVER mix languages
- Prompt Template — *specifies output format* — ONLY use: <h1>, <h2>, <ul>, <li>, <p>, <strong>, <b>, and plain text
- Prompt Template — *specifies output format* — NO markdown
- Prompt Template — *includes data* — INPUT DATA
- Prompt Template — *includes data* — COST DATA
- Prompt Template — *includes data* — LOCAL RESEARCH
- Prompt Template — *aims to create* — itinerary
- itinerary — *respects* — budget constraint
- itinerary — *aligns with* — user interests
- itinerary — *adapts to* — weather conditions
- itinerary — *includes* — hidden gems
- Slide 15 — *is preceded by* — Slide 14 - The Aggregate Node (Data Fusion)
- Slide 15 — *is followed by* — Slide 16 - Running the Graph
- Slide 15 — *links to* — Index

%% ai-graph-end %%