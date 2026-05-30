---
ai_hash: 890d216c18e1b018
ai_model: google/gemini-2.5-flash
ai_updated: '2026-05-30'
entities:
- Slide 37
- Monitoring
- Alerting
- Metrics
- Prometheus
- Counter
- Histogram
- Gauge
- Success/failure rates
- Latency
- Queue depth
- trip_plans_successful
- trip_plans_failed
- trip_plan_duration_seconds
- pending_plans
- send_alert
- Slide 36 - Tool Composition
- Index
- Slide 38 - Real-World Adaptations
slide_number: 37
source: https://github.com/trieu/ai-trip-planner/blob/main/docs/AGENTIC_PROGRAMMING_LECTURE.md
tags:
- lecture/slide
- langgraph
- agentic-programming
title: 'Slide 37: Monitoring & Alerting'
---

# Slide 37: Monitoring & Alerting

### Metrics to Track

```python
from prometheus_client import Counter, Histogram, Gauge

# Success/failure rates
success_counter = Counter(
    'trip_plans_successful',
    'Total successful trip plans'
)
failure_counter = Counter(
    'trip_plans_failed',
    'Total failed trip plans'
)

# Latency
plan_duration = Histogram(
    'trip_plan_duration_seconds',
    'Time to generate a trip plan'
)

# Queue depth
queue_depth = Gauge(
    'pending_plans',
    'Number of plans waiting to be processed'
)

def _record_execution(node_name, duration, success):
    if success:
        success_counter.inc()
    else:
        failure_counter.inc()
    
    plan_duration.observe(duration)
```

### Alerting

```python
# Alert if latency exceeds threshold
if plan_duration > 30_000:  # 30 seconds
    send_alert("Trip planner slow!", f"Duration: {plan_duration}ms")

# Alert if failure rate > 5%
if failure_counter.count / (success_counter.count + failure_counter.count) > 0.05:
    send_alert("High failure rate!", "Check error logs")
```

---

← [[Slide 36 - Tool Composition|Previous]] · [[Index|🏠 Index]] · [[Slide 38 - Real-World Adaptations|Next]] →

%% ai-graph-start %%

**Related notes:**
- [[Slide 38 - Real-World Adaptations]]
- [[Slide 17 - Observability & Tracing]]
- [[Slide 33 - Debugging Techniques]]

**Relations:**
- Slide 37 — *discusses* — Monitoring
- Slide 37 — *discusses* — Alerting
- Monitoring — *involves* — Metrics
- Metrics — *include* — Success/failure rates
- Metrics — *include* — Latency
- Metrics — *include* — Queue depth
- Prometheus — *provides* — Counter
- Prometheus — *provides* — Histogram
- Prometheus — *provides* — Gauge
- trip_plans_successful — *is a* — Counter
- trip_plans_failed — *is a* — Counter
- trip_plan_duration_seconds — *is a* — Histogram
- pending_plans — *is a* — Gauge
- Success/failure rates — *tracked by* — trip_plans_successful
- Success/failure rates — *tracked by* — trip_plans_failed
- Latency — *tracked by* — trip_plan_duration_seconds
- Queue depth — *tracked by* — pending_plans
- Alerting — *monitors* — Latency
- Alerting — *monitors* — Success/failure rates
- Alerting — *uses* — send_alert
- Latency — *triggers alert if* — exceeds threshold
- Success/failure rates — *triggers alert if* — failure rate > 5%
- Slide 37 — *follows* — Slide 36 - Tool Composition
- Slide 37 — *precedes* — Slide 38 - Real-World Adaptations
- Slide 37 — *links to* — Index

%% ai-graph-end %%