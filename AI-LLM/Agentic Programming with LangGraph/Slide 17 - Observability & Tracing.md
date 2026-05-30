---
ai_hash: 830236db4ff677c3
ai_model: google/gemini-2.5-flash
ai_updated: '2026-05-30'
entities:
- Observability
- Tracing
- Agentic Systems
- Node
- Inputs
- Outputs
- Tools
- Latency bottlenecks
- LLM calls
- Arize Phoenix
- OpenTelemetry
- TracerProvider
- BatchSpanProcessor
- OTLPSpanExporter
- LangChainInstrumentor
- LangChain
- using_attributes
- setup_observability
- safe_attributes
- PHOENIX_COLLECTOR_ENDPOINT
- _profile_node
- TripState
- user_id
- profile_service
- get_user_profile
- Phoenix Dashboard
- TripPlannerWorkflow
- load_profile
- PostgreSQL
- research
- get_destination_info
- Web Search
- weather
- get_current_weather
- API
- budget
- get_costs
- Database
- aggregate
- journey_plan
- LLM.ainvoke
- Claude Opus
- Sequential processing
- Context manager
slide_number: 17
source: https://github.com/trieu/ai-trip-planner/blob/main/docs/AGENTIC_PROGRAMMING_LECTURE.md
tags:
- lecture/slide
- langgraph
- agentic-programming
title: 'Slide 17: Observability & Tracing'
---

# Slide 17: Observability & Tracing

### Why Tracing Matters

Debug agentic systems by seeing:
- ✅ Which node ran when
- ✅ What inputs/outputs each node received
- ✅ Which tools were called
- ✅ Where latency bottlenecks exist
- ✅ Which LLM calls happened

### Integration with Arize Phoenix

```python
# backend/core_llm/observer_utils.py
from opentelemetry import trace
from opentelemetry.sdk.trace import TracerProvider
from opentelemetry.sdk.trace.export import BatchSpanProcessor
from opentelemetry.exporter.otlp.proto.http.trace_exporter import OTLPSpanExporter
from openinference.instrumentation.langchain import LangChainInstrumentor
from openinference.instrumentation import using_attributes

def setup_observability():
    endpoint = Settings().PHOENIX_COLLECTOR_ENDPOINT
    if not endpoint:
        return

    tracer_provider = TracerProvider()
    tracer_provider.add_span_processor(
        BatchSpanProcessor(OTLPSpanExporter(endpoint=endpoint))
    )
    trace.set_tracer_provider(tracer_provider)

    # Automatically traces LangChain model/tool calls.
    LangChainInstrumentor().instrument()

def safe_attributes(attributes: dict):
    """Context manager for adding custom span attributes."""
    return using_attributes(attributes=attributes)
```

### Usage in Nodes

```python
def _profile_node(self, state: TripState) -> Dict[str, Any]:
    user_id = state["trip_request"].get("user_id")
    
    with safe_attributes({"agent.type": "profile_loader", "user.id": user_id}):
        profile = self.profile_service.get_user_profile(user_id)
    
    return {"user_profile": profile}
```

### Phoenix Dashboard Shows

```
TripPlannerWorkflow
├── load_profile (50ms)
│   └── get_user_profile() → PostgreSQL (45ms)
├── [Parallel]
│   ├── research (2000ms)
│   │   └── get_destination_info() → Web Search (1950ms)
│   ├── weather (100ms)
│   │   └── get_current_weather() → API (80ms)
│   └── budget (150ms)
│       └── get_costs() → Database (140ms)
├── aggregate (10ms)
└── journey_plan (3000ms)
    └── LLM.ainvoke() → Claude Opus (2950ms)

Total: 5110ms (saved 1600ms+ vs sequential!)
```

---

← [[Slide 16 - Running the Graph|Previous]] · [[Index|🏠 Index]] · [[Slide 18 - Error Handling in Agentic Systems|Next]] →

%% ai-graph-start %%

**Related notes:**
- [[Slide 33 - Debugging Techniques]]
- [[Slide 40 - Recap & Your Turn]]
- [[Slide 1 - Course Overview]]
- [[Index]]
- [[Slide 16 - Running the Graph]]

**Relations:**
- Tracing — *debugs* — Agentic Systems
- Tracing — *reveals* — Node execution
- Tracing — *reveals* — Inputs for Node
- Tracing — *reveals* — Outputs from Node
- Tracing — *reveals* — Tools invoked
- Tracing — *identifies* — Latency bottlenecks
- Tracing — *monitors* — LLM calls
- Arize Phoenix — *integrates with* — OpenTelemetry
- setup_observability — *configures* — OpenTelemetry
- setup_observability — *uses* — TracerProvider
- TracerProvider — *uses* — BatchSpanProcessor
- BatchSpanProcessor — *exports spans via* — OTLPSpanExporter
- OTLPSpanExporter — *sends data to* — PHOENIX_COLLECTOR_ENDPOINT
- setup_observability — *instruments* — LangChainInstrumentor
- LangChainInstrumentor — *traces* — LangChain
- safe_attributes — *is a* — Context manager
- safe_attributes — *uses* — using_attributes
- _profile_node — *is a type of* — Node
- _profile_node — *uses* — safe_attributes
- _profile_node — *calls* — profile_service
- profile_service — *provides* — get_user_profile
- get_user_profile — *retrieves* — user_id
- Phoenix Dashboard — *visualizes* — TripPlannerWorkflow
- TripPlannerWorkflow — *contains step* — load_profile
- load_profile — *calls* — get_user_profile
- get_user_profile — *accesses* — PostgreSQL
- TripPlannerWorkflow — *contains step* — research
- research — *calls* — get_destination_info
- get_destination_info — *uses* — Web Search
- TripPlannerWorkflow — *contains step* — weather
- weather — *calls* — get_current_weather
- get_current_weather — *uses* — API
- TripPlannerWorkflow — *contains step* — budget
- budget — *calls* — get_costs
- get_costs — *accesses* — Database
- TripPlannerWorkflow — *contains step* — aggregate
- TripPlannerWorkflow — *contains step* — journey_plan
- journey_plan — *calls* — LLM.ainvoke
- LLM.ainvoke — *uses* — Claude Opus
- TripPlannerWorkflow — *is more efficient than* — Sequential processing

%% ai-graph-end %%