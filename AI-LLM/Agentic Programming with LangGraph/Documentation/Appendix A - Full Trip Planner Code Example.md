---
title: "Appendix A: Full Trip Planner Code Example"
tags: [lecture/appendix, langgraph, agentic-programming]
source: https://github.com/trieu/ai-trip-planner/blob/main/docs/AGENTIC_PROGRAMMING_LECTURE.md
---

# Appendix A: Full Trip Planner Code Example

### Complete SmartTripPlanner Implementation

```python
# backend/core_llm/smart_trip_planner.py (simplified)

import logging
from typing import Dict, Any
from langchain_core.messages import SystemMessage, HumanMessage
from langgraph.graph import StateGraph, END, START
from core_llm.state_models import TripState
from core_llm.meta_llm import MetaLLM
from core_llm.prompt_builder import build_trip_planner_prompt
from services import DataServiceFactory
from tools.travel_tools import get_destination_info, get_costs
from tools.weather_tools import get_current_weather
from tools.text_utils import deduplicate_tool_calls

logger = logging.getLogger(__name__)

class SmartTripPlanner:
    def __init__(self):
        self.llm = MetaLLM.get_llm(temperature=0.7)
        self.profile_service = DataServiceFactory.get_service()
        self.app = self._build_graph()
    
    def _build_graph(self):
        builder = StateGraph(TripState)
        
        builder.add_node("load_profile", self._profile_node)
        builder.add_node("research", self._research_node)
        builder.add_node("weather", self._weather_node)
        builder.add_node("budget", self._budget_node)
        builder.add_node("aggregate", self._aggregate_node)
        builder.add_node("journey_plan", self._journey_plan_node)
        
        # Edges
        builder.add_edge(START, "load_profile")
        builder.add_edge("load_profile", "research")
        builder.add_edge("load_profile", "weather")
        builder.add_edge("load_profile", "budget")
        builder.add_edge("research", "aggregate")
        builder.add_edge("weather", "aggregate")
        builder.add_edge("budget", "aggregate")
        builder.add_edge("aggregate", "journey_plan")
        builder.add_edge("journey_plan", END)
        
        return builder.compile()
    
    def _profile_node(self, state: TripState) -> Dict[str, Any]:
        user_id = state["trip_request"].get("user_id")
        interests = state["trip_request"].get("interests", "")
        
        profile = {}
        if user_id:
            profile = self.profile_service.get_user_profile(user_id)
        
        profile.setdefault("current_interests", interests.split(",") if interests else [])
        return {"user_profile": profile}
    
    async def _research_node(self, state: TripState) -> Dict[str, Any]:
        dest = state["trip_request"]["destination"]
        
        try:
            summary = await get_destination_info.ainvoke({
                "destination": dest
            })
        except Exception as e:
            logger.error(f"Research failed: {e}")
            summary = f"Unable to fetch research for {dest}"
        
        return {
            "research": summary,
            "tool_calls": [{"tool": "get_destination_info", "args": {"destination": dest}}]
        }
    
    def _weather_node(self, state: TripState) -> Dict[str, Any]:
        dest = state["trip_request"]["destination"]
        
        try:
            weather = get_current_weather.invoke({"location": dest})
        except Exception as e:
            logger.error(f"Weather failed: {e}")
            weather = "Weather data unavailable"
        
        return {
            "weather": weather,
            "tool_calls": [{"tool": "get_current_weather", "args": {"location": dest}}]
        }
    
    async def _budget_node(self, state: TripState) -> Dict[str, Any]:
        dest = state["trip_request"]["destination"]
        budget_level = state["trip_request"].get("budget", "moderate")
        
        try:
            costs = await get_costs.ainvoke({
                "destination": dest,
                "budget_level": budget_level
            })
        except Exception as e:
            logger.error(f"Budget failed: {e}")
            costs = "Cost analysis unavailable"
        
        return {
            "budget": costs,
            "tool_calls": [{
                "tool": "get_costs",
                "args": {"destination": dest, "budget_level": budget_level}
            }]
        }
    
    def _aggregate_node(self, state: TripState) -> Dict[str, Any]:
        # Combine all parallel outputs by cleaning metadata.
        # The journey planner reads research/weather/budget directly from state.
        return {"tool_calls": deduplicate_tool_calls(state.get("tool_calls", []))}
    
    def _journey_plan_node(self, state: TripState) -> Dict[str, Any]:
        prompt = build_trip_planner_prompt(state)
        
        response = self.llm.invoke([
            SystemMessage(content="You are a travel planner."),
            HumanMessage(prompt)
        ])
        
        return {
            "final": response.content
        }
    
    async def plan(self, trip_request: Dict[str, Any]) -> Dict[str, Any]:
        """Main entry point for planning a trip."""
        initial_state = {
            "trip_request": trip_request,
            "messages": [],
            "tool_calls": [],
            "user_profile": {},
        }
        
        return await self.app.ainvoke(initial_state)

# Usage
if __name__ == "__main__":
    import asyncio
    
    planner = SmartTripPlanner()
    
    request = {
        "destination": "Tokyo, Japan",
        "duration": "7 days",
        "budget": "$2000",
        "interests": "food, culture",
        "user_id": "user_123"
    }
    
    result = asyncio.run(planner.plan(request))
    print(result["final"])
```

---

← [[Slide 40 - Recap & Your Turn|Previous]] · [[Index|🏠 Index]] · [[Appendix B - Quick Reference|Next]] →
