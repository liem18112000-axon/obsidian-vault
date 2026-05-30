---
ai_hash: 68e8fb7939579ec9
ai_model: google/gemini-2.5-flash
ai_updated: '2026-05-30'
entities:
- Profile Node
- Personalization
- User Context
- Downstream Agents
- Dietary restrictions
- Travel style
- Past trips
- Payment method
- _profile_node function
- TripState
- user_id
- interests
- Profile Service
- CDP
- PostgreSQL
- Mock
- Data Service Factory
- Strategy Pattern
- LEOCDPService
- PostgresProfileService
- MockTestService
- Node Logic
- Slide 9
- Slide 10
- Slide 11
- Index
slide_number: 10
source: https://github.com/trieu/ai-trip-planner/blob/main/docs/AGENTIC_PROGRAMMING_LECTURE.md
tags:
- lecture/slide
- langgraph
- agentic-programming
title: 'Slide 10: The Profile Node (Personalization)'
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

%% ai-graph-start %%

**Related notes:**
- [[Slide 9 - Writing Node Functions]]
- [[Slide 34 - Building Adaptive Agents]]
- [[Slide 21 - Testing Agentic Systems]]
- [[Slide 15 - The Journey Plan Node (LLM Synthesis)]]
- [[Slide 19 - Conditional Branching (Advanced)]]

**Relations:**
- Profile Node — *enables* — Personalization
- Profile Node — *provides* — User Context
- User Context — *is needed by* — Downstream Agents
- User Context — *includes* — Dietary restrictions
- User Context — *includes* — Travel style
- User Context — *includes* — Past trips
- User Context — *includes* — Payment method
- _profile_node function — *implements* — Profile Node
- _profile_node function — *receives* — TripState
- _profile_node function — *extracts* — user_id
- _profile_node function — *extracts* — interests
- _profile_node function — *uses* — Profile Service
- Profile Service — *can fetch from* — CDP
- Profile Service — *can fetch from* — PostgreSQL
- Profile Service — *can fetch from* — Mock
- Data Service Factory — *implements* — Strategy Pattern
- Data Service Factory — *provides instances of* — Profile Service
- Data Service Factory — *returns* — LEOCDPService
- Data Service Factory — *returns* — PostgresProfileService
- Data Service Factory — *returns* — MockTestService
- Data Service Factory — *enables changing backends without altering* — Node Logic
- Slide 10 — *follows* — Slide 9
- Slide 10 — *precedes* — Slide 11
- Slide 10 — *links to* — Index

%% ai-graph-end %%