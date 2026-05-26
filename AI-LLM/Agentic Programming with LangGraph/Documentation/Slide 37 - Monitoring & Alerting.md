---
title: "Slide 37: Monitoring & Alerting"
slide_number: 37
tags: [lecture/slide, langgraph, agentic-programming]
source: https://github.com/trieu/ai-trip-planner/blob/main/docs/AGENTIC_PROGRAMMING_LECTURE.md
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
