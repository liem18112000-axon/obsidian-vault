---
title: "Slide 10: The Profile Node (Personalization)"
slide_number: 10
tags: [lecture/slide, langgraph, agentic-programming]
source: https://github.com/trieu/ai-trip-planner/blob/main/docs/AGENTIC_PROGRAMMING_LECTURE.md
---

# Slide 10: The Profile Node (Personalization)

### Why Load Profile First?

All downstream agents need user context:
- Dietary restrictions (vegan? allergies?)
- Travel style (luxury vs budget? solo vs family?)
- Past trips (avoid repeats?)
- Payment method (cryptocurrency? credit card?)

### The Implementation

```python
def _profile_node(self, state: TripState) -> Dict[str, Any]:
    # Extract user_id from request
    user_id = state["trip_request"].get("user_id")
    interests = state["trip_request"].get("interests")
    
    profile = {}
    
    # Fetch from data service (could be CDP, PostgreSQL, mock)
    if user_id:
        profile = self.profile_service.get_user_profile(user_id)
    
    # Set defaults
    profile.setdefault(
        "current_interests",
        interests.split(",") if interests else []
    )
    
    # Return only profile updates
    return {"user_profile": profile}
```

### Data Service Factory (Strategy Pattern)

```python
# backend/services/data_service.py
class DataServiceFactory:
    @staticmethod
    def get_service():
        if os.getenv("USE_CDP") == "true":
            return LEOCDPService()  # Real CDP API
        elif os.getenv("USE_POSTGRES") == "true":
            return PostgresProfileService()  # PostgreSQL
        else:
            return MockTestService()  # For local testing
```

✅ **Key Insight:** Change backends without changing node logic!

---

← [[Slide 9 - Writing Node Functions|Previous]] · [[Index|🏠 Index]] · [[Slide 11 - The Research Node (Tool Integration)|Next]] →
