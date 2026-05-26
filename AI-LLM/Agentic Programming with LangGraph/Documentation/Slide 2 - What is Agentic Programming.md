---
title: "Slide 2: What is Agentic Programming?"
slide_number: 2
tags: [lecture/slide, langgraph, agentic-programming]
source: https://github.com/trieu/ai-trip-planner/blob/main/docs/AGENTIC_PROGRAMMING_LECTURE.md
---

# Slide 2: What is Agentic Programming?

### Traditional LLM Approach (Single Call)

```
User Input → LLM → Output
```

❌ **Limitations:**
- One-shot responses
- No tool use or external data
- Limited reasoning chains
- Can't decompose complex tasks

### Agentic Approach (Orchestrated System)

```
User Input → Agent 1 ← Tools → Agent 2 ← Data
              ↓                    ↓
         Orchestrator ← State Management
              ↓
           Output
```

✅ **Benefits:**
- Multi-step reasoning
- Specialized agents for different tasks
- Dynamic tool calling
- Parallel execution for speed

---

← [[Slide 1 - Course Overview|Previous]] · [[Index|🏠 Index]] · [[Slide 3 - The AI Trip Planner - Your Real-World Blueprint|Next]] →
