---
title: "Appendix B: Quick Reference"
tags: [lecture/appendix, langgraph, agentic-programming]
source: https://github.com/trieu/ai-trip-planner/blob/main/docs/AGENTIC_PROGRAMMING_LECTURE.md
---

# Appendix B: Quick Reference

### StateGraph API Cheat Sheet

```python
# Create
builder = StateGraph(YourStateType)

# Add nodes
builder.add_node("name", function_or_callable)

# Add edges
builder.add_edge(START, "first_node")
builder.add_edge("node_a", "node_b")
builder.add_edge("node_c", END)

# Conditional edges
builder.add_conditional_edges("node", routing_fn, {"path1": "node1", "path2": "node2"})

# Compile
app = builder.compile()

# Run
result = app.invoke(initial_state)  # Sync
result = await app.ainvoke(initial_state)  # Async

# Stream
for step in app.stream(initial_state):
    print(step)

# Check graph structure
print(app.get_graph().draw_mermaid())
```

### Common Imports

```python
from typing import Dict, Any, Optional, List
from typing_extensions import TypedDict, Annotated
import operator

from langgraph.graph import StateGraph, START, END
from langchain_core.messages import BaseMessage, SystemMessage, HumanMessage
from langchain.tools import tool
from pydantic import BaseModel, Field
```

---

**End of Lecture** 🎓

*Last updated: 2026-05-24*  
*For questions: See LangGraph docs or raise an issue in the AI Trip Planner repo*

---

← [[Appendix A - Full Trip Planner Code Example|Previous]] · [[Index|🏠 Index]]
