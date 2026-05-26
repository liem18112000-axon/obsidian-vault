---
title: "Slide 17: Observability & Tracing"
slide_number: 17
tags: [lecture/slide, langgraph, agentic-programming]
source: https://github.com/trieu/ai-trip-planner/blob/main/docs/AGENTIC_PROGRAMMING_LECTURE.md
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
