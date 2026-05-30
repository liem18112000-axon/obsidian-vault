---
ai_hash: 2fd1bfa1333fdf60
ai_model: google/gemini-2.5-flash
ai_updated: '2026-05-30'
entities:
- Appendix B
- Quick Reference
- StateGraph API Cheat Sheet
- StateGraph
- builder
- add_node
- add_edge
- START
- END
- add_conditional_edges
- compile
- app
- invoke
- ainvoke
- stream
- get_graph
- draw_mermaid
- Common Imports
- typing
- Dict
- Any
- Optional
- List
- typing_extensions
- TypedDict
- Annotated
- operator
- langgraph.graph
- langchain_core.messages
- BaseMessage
- SystemMessage
- HumanMessage
- langchain.tools
- tool
- pydantic
- BaseModel
- Field
- LangGraph docs
- AI Trip Planner repo
- Appendix A - Full Trip Planner Code Example
- Index
source: https://github.com/trieu/ai-trip-planner/blob/main/docs/AGENTIC_PROGRAMMING_LECTURE.md
tags:
- lecture/appendix
- langgraph
- agentic-programming
title: 'Appendix B: Quick Reference'
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

%% ai-graph-start %%

**Related notes:**
- [[Slide 7 - Building the Graph]]
- [[Slide 1 - Course Overview]]
- [[Index]]
- [[Slide 16 - Running the Graph]]
- [[Slide 6 - State Definition in LangGraph]]

**Relations:**
- Appendix B — *is a* — Quick Reference
- StateGraph API Cheat Sheet — *describes usage of* — StateGraph
- StateGraph — *creates instance* — builder
- builder — *has method* — add_node
- builder — *has method* — add_edge
- builder — *has method* — add_conditional_edges
- builder — *has method* — compile
- compile — *returns* — app
- app — *has method* — invoke
- app — *has method* — ainvoke
- app — *has method* — stream
- app — *has method* — get_graph
- get_graph — *has method* — draw_mermaid
- Common Imports — *includes module* — typing
- Common Imports — *includes module* — typing_extensions
- Common Imports — *includes module* — operator
- Common Imports — *includes module* — langgraph.graph
- Common Imports — *includes module* — langchain_core.messages
- Common Imports — *includes module* — langchain.tools
- Common Imports — *includes module* — pydantic
- typing — *exports* — Dict
- typing — *exports* — Any
- typing — *exports* — Optional
- typing — *exports* — List
- typing_extensions — *exports* — TypedDict
- typing_extensions — *exports* — Annotated
- langgraph.graph — *exports* — StateGraph
- langgraph.graph — *exports* — START
- langgraph.graph — *exports* — END
- langchain_core.messages — *exports* — BaseMessage
- langchain_core.messages — *exports* — SystemMessage
- langchain_core.messages — *exports* — HumanMessage
- langchain.tools — *exports* — tool
- pydantic — *exports* — BaseModel
- pydantic — *exports* — Field
- LangGraph docs — *is resource for* — questions
- AI Trip Planner repo — *is resource for* — questions
- Appendix A - Full Trip Planner Code Example — *is linked as* — Previous
- Index — *is linked as* — Home

%% ai-graph-end %%