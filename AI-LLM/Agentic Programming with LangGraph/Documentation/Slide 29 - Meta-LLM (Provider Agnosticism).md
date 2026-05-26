---
title: "Slide 29: Meta-LLM (Provider Agnosticism)"
slide_number: 29
tags: [lecture/slide, langgraph, agentic-programming]
source: https://github.com/trieu/ai-trip-planner/blob/main/docs/AGENTIC_PROGRAMMING_LECTURE.md
---

# Slide 29: Meta-LLM (Provider Agnosticism)

### Why Swap LLM Providers?

- Different costs (GPT-4 vs Claude vs Gemini)
- Different latency profiles
- Regional availability
- Different capabilities

### The MetaLLM Factory

```python
# backend/core_llm/meta_llm.py
from langchain_openai import ChatOpenAI
from langchain_google_genai import ChatGoogleGenerativeAI

class MetaLLM:
    @staticmethod
    def get_llm(temperature: float = 0.7):
        provider = os.getenv("LLM_PROVIDER", "GOOGLE_GEMINI").upper()
        model_name = os.getenv("LLM_MODEL_NAME")
        
        if provider == "OPENAI":
            return ChatOpenAI(
                model=model_name or "gpt-4o",
                temperature=temperature,
                api_key=os.getenv("OPENAI_API_KEY")
            )
        
        else:
            return ChatGoogleGenerativeAI(
                model=model_name or "gemini-2.5-flash-lite",
                temperature=temperature,
                api_key=os.getenv("GOOGLE_GEMINI_API_KEY")
            )
```

### Usage

```bash
# Swap provider with env var, no code change
export LLM_PROVIDER=OPENAI
export LLM_MODEL_NAME=gpt-4o
python -m uvicorn main:app --reload

# Or use the default Gemini path:
export LLM_PROVIDER=GOOGLE_GEMINI
export LLM_MODEL_NAME=gemini-2.5-flash-lite
```

---

← [[Slide 28 - Prompt Engineering for Agentic Systems|Previous]] · [[Index|🏠 Index]] · [[Slide 30 - RAG (Retrieval-Augmented Generation)|Next]] →
