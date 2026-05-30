---
ai_hash: 36bc8b40f9783d86
ai_model: google/gemini-2.5-flash
ai_updated: '2026-05-30'
entities:
- Course Overview
- Agentic Programming
- LLM calls
- orchestrated AI systems
- LangGraph Fundamentals
- directed state graphs
- agent workflows
- State Management
- data
- agents
- Parallel Execution
- AI systems
- Tool Integration
- APIs
- databases
- external tools
- AI Trip Planner system
- production patterns
- Python basics
- functions
- classes
- async/await
- LLM APIs
- OpenAI
- Gemini
- LangGraph experience
- concept slide
- repo file
- code
- node
- graph
- field
- tool call
- fallback
- state
- dict
- '`backend/core_llm/smart_trip_planner.py`'
- graph structure
- node functions
- '`backend/core_llm/state_models.py`'
- LangGraph state schema
- '`backend/core_llm/prompt_builder.py`'
- final itinerary prompt
- '`backend/tools/travel_tools.py`'
- destination tools
- budget tools
- '`backend/tools/weather_tools.py`'
- weather tool
- '`backend/core_llm/observer_utils.py`'
- tracing
- observability setup
- research
- weather
- budget
- shared state
- state update
- mutating state
- failed tool call
- workflow
- frontend request
- final itinerary
- Index
- Slide 2 - What is Agentic Programming
excalidraw-plugin: parsed
slide_number: 1
source: https://github.com/trieu/ai-trip-planner/blob/main/docs/AGENTIC_PROGRAMMING_LECTURE.md
tags:
- lecture/slide
- langgraph
- agentic-programming
title: 'Slide 1: Course Overview'
---

# Slide 1: Course Overview

### What You'll Learn

- 🤖 **What is Agentic Programming?** Move beyond single LLM calls to orchestrated AI systems
- 🗺️ **LangGraph Fundamentals**: Build directed state graphs for agent workflows
- 🔄 **State Management**: How to pass data between agents without conflicts
- ⚡ **Parallel Execution**: Speed up AI systems by running agents simultaneously
- 🛠️ **Tool Integration**: Teach agents to call APIs, databases, and external tools
- 🏗️ **Real-World Example**: The AI Trip Planner system that demonstrates production patterns

### Prerequisites

- Python basics (functions, classes, async/await)
- Understanding of LLM APIs (OpenAI, Gemini, etc.)
- No LangGraph experience needed—we'll build it together!

### How to Self-Study This Lecture

Use this as a hands-on reading path, not just slide material:

1. **Read the concept slide** to understand the pattern.
2. **Open the linked repo file** and compare the slide with real code.
3. **Run or inspect one node at a time** before running the full graph.
4. **Change one small thing** after each section: add a field, add a tool call, or add a fallback.
5. **Trace the state** by printing or logging the dict returned from each node.

### Repo Files to Keep Open

- `backend/core_llm/smart_trip_planner.py` — graph structure and node functions
- `backend/core_llm/state_models.py` — LangGraph state schema
- `backend/core_llm/prompt_builder.py` — final itinerary prompt
- `backend/tools/travel_tools.py` — destination and budget tools
- `backend/tools/weather_tools.py` — weather tool
- `backend/core_llm/observer_utils.py` — tracing and observability setup

### Learning Checkpoints

By the end, you should be able to:

- Explain why `research`, `weather`, and `budget` can run in parallel.
- Add a new node without breaking shared state.
- Know when to return a state update versus mutating state.
- Debug a failed tool call without crashing the whole workflow.
- Describe how the frontend request becomes a final itinerary.

---

[[Index|🏠 Index]] · [[Slide 2 - What is Agentic Programming|Next]] →

%% ai-graph-start %%

**Related notes:**
- [[Index]]
- [[Slide 40 - Recap & Your Turn]]
- [[Slide 3 - The AI Trip Planner - Your Real-World Blueprint]]
- [[Slide 7 - Building the Graph]]
- [[Appendix B - Quick Reference]]

**Relations:**
- Course Overview — *covers* — Agentic Programming
- Course Overview — *covers* — LangGraph Fundamentals
- Course Overview — *covers* — State Management
- Course Overview — *covers* — Parallel Execution
- Course Overview — *covers* — Tool Integration
- Course Overview — *covers* — AI Trip Planner system
- Agentic Programming — *moves beyond* — LLM calls
- Agentic Programming — *involves* — orchestrated AI systems
- LangGraph Fundamentals — *builds* — directed state graphs
- directed state graphs — *are for* — agent workflows
- State Management — *passes* — data
- State Management — *between* — agents
- State Management — *without* — conflicts
- Parallel Execution — *speeds up* — AI systems
- Parallel Execution — *runs* — agents simultaneously
- Tool Integration — *teaches agents to call* — APIs
- Tool Integration — *teaches agents to call* — databases
- Tool Integration — *teaches agents to call* — external tools
- AI Trip Planner system — *demonstrates* — production patterns
- Python basics — *is a prerequisite for* — Course Overview
- Python basics — *includes* — functions
- Python basics — *includes* — classes
- Python basics — *includes* — async/await
- Understanding of LLM APIs — *is a prerequisite for* — Course Overview
- LLM APIs — *include* — OpenAI
- LLM APIs — *include* — Gemini
- LangGraph experience — *is not needed for* — Course Overview
- How to Self-Study This Lecture — *recommends* — Read the concept slide
- How to Self-Study This Lecture — *recommends* — Open the linked repo file
- How to Self-Study This Lecture — *recommends* — Run or inspect one node at a time
- How to Self-Study This Lecture — *recommends* — Change one small thing
- How to Self-Study This Lecture — *recommends* — Trace the state
- `backend/core_llm/smart_trip_planner.py` — *contains* — graph structure
- `backend/core_llm/smart_trip_planner.py` — *contains* — node functions
- `backend/core_llm/state_models.py` — *contains* — LangGraph state schema
- `backend/core_llm/prompt_builder.py` — *contains* — final itinerary prompt
- `backend/tools/travel_tools.py` — *contains* — destination tools
- `backend/tools/travel_tools.py` — *contains* — budget tools
- `backend/tools/weather_tools.py` — *contains* — weather tool
- `backend/core_llm/observer_utils.py` — *contains* — tracing
- `backend/core_llm/observer_utils.py` — *contains* — observability setup
- Learning Checkpoints — *include ability to* — Explain why research, weather, and budget can run in parallel
- Learning Checkpoints — *include ability to* — Add a new node without breaking shared state
- Learning Checkpoints — *include ability to* — Know when to return a state update versus mutating state
- Learning Checkpoints — *include ability to* — Debug a failed tool call without crashing the whole workflow
- Learning Checkpoints — *include ability to* — Describe how the frontend request becomes a final itinerary
- Course Overview — *links to* — Index
- Course Overview — *is followed by* — Slide 2 - What is Agentic Programming

%% ai-graph-end %%