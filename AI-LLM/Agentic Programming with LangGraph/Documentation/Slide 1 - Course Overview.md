---
title: "Slide 1: Course Overview"
slide_number: 1
tags: [lecture/slide, langgraph, agentic-programming]
source: https://github.com/trieu/ai-trip-planner/blob/main/docs/AGENTIC_PROGRAMMING_LECTURE.md
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
