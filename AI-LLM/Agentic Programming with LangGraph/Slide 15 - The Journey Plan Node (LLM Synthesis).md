---
title: "Slide 15: The Journey Plan Node (LLM Synthesis)"
slide_number: 15
tags: [lecture/slide, langgraph, agentic-programming]
source: https://github.com/trieu/ai-trip-planner/blob/main/docs/AGENTIC_PROGRAMMING_LECTURE.md
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
