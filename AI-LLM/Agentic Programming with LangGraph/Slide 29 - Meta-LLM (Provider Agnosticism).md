---
ai_hash: e6b47ad108c843c2
ai_model: google/gemini-2.5-flash
ai_updated: '2026-05-30'
entities:
- Slide 29
- Meta-LLM
- Provider Agnosticism
- LLM Providers
- Costs
- Latency Profiles
- Regional Availability
- Capabilities
- The MetaLLM Factory
- '`backend/core_llm/meta_llm.py`'
- '`langchain_openai`'
- '`ChatOpenAI`'
- '`langchain_google_genai`'
- '`ChatGoogleGenerativeAI`'
- '`MetaLLM` (class)'
- '`get_llm` (method)'
- '`temperature` (parameter)'
- '`os.getenv`'
- '`LLM_PROVIDER` (environment variable)'
- '`LLM_MODEL_NAME` (environment variable)'
- '`OPENAI` (provider)'
- '`GOOGLE_GEMINI` (provider)'
- '`gpt-4o` (model)'
- '`gemini-2.5-flash-lite` (model)'
- '`OPENAI_API_KEY` (environment variable)'
- '`GOOGLE_GEMINI_API_KEY` (environment variable)'
- Usage (section)
- '`export LLM_PROVIDER=OPENAI` (command)'
- '`export LLM_MODEL_NAME=gpt-4o` (command)'
- '`export LLM_PROVIDER=GOOGLE_GEMINI` (command)'
- '`export LLM_MODEL_NAME=gemini-2.5-flash-lite` (command)'
- '`python -m uvicorn main:app --reload` (command)'
- Slide 28 - Prompt Engineering for Agentic Systems
- Index
- Slide 30 - RAG (Retrieval-Augmented Generation)
slide_number: 29
source: https://github.com/trieu/ai-trip-planner/blob/main/docs/AGENTIC_PROGRAMMING_LECTURE.md
tags:
- lecture/slide
- langgraph
- agentic-programming
title: 'Slide 29: Meta-LLM (Provider Agnosticism)'
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

%% ai-graph-start %%

**Related notes:**
- [[Slide 31 - Scaling & Performance Optimization]]
- [[Slide 28 - Prompt Engineering for Agentic Systems]]
- [[Slide 30 - RAG (Retrieval-Augmented Generation)]]
- [[Slide 26 - Working with Custom Tools]]

**Relations:**
- Slide 29 — *discusses* — Meta-LLM
- Meta-LLM — *enables* — Provider Agnosticism
- Provider Agnosticism — *involves swapping* — LLM Providers
- LLM Providers — *differ by* — Costs
- LLM Providers — *differ by* — Latency Profiles
- LLM Providers — *differ by* — Regional Availability
- LLM Providers — *differ by* — Capabilities
- The MetaLLM Factory — *is an implementation of* — Meta-LLM
- `backend/core_llm/meta_llm.py` — *defines* — `MetaLLM` (class)
- `MetaLLM` (class) — *imports* — `langchain_openai`
- `MetaLLM` (class) — *imports* — `langchain_google_genai`
- `ChatOpenAI` — *is from* — `langchain_openai`
- `ChatGoogleGenerativeAI` — *is from* — `langchain_google_genai`
- `MetaLLM` (class) — *has method* — `get_llm` (method)
- `get_llm` (method) — *takes parameter* — `temperature` (parameter)
- `get_llm` (method) — *uses* — `os.getenv`
- `os.getenv` — *retrieves* — `LLM_PROVIDER` (environment variable)
- `os.getenv` — *retrieves* — `LLM_MODEL_NAME` (environment variable)
- `os.getenv` — *retrieves* — `OPENAI_API_KEY` (environment variable)
- `os.getenv` — *retrieves* — `GOOGLE_GEMINI_API_KEY` (environment variable)
- `LLM_PROVIDER` (environment variable) — *can be* — `OPENAI` (provider)
- `LLM_PROVIDER` (environment variable) — *can be* — `GOOGLE_GEMINI` (provider)
- `OPENAI` (provider) — *uses model* — `gpt-4o` (model)
- `GOOGLE_GEMINI` (provider) — *uses model* — `gemini-2.5-flash-lite` (model)
- `get_llm` (method) — *returns instance of* — `ChatOpenAI`
- `get_llm` (method) — *returns instance of* — `ChatGoogleGenerativeAI`
- Usage (section) — *demonstrates setting* — `LLM_PROVIDER` (environment variable)
- Usage (section) — *demonstrates setting* — `LLM_MODEL_NAME` (environment variable)
- Usage (section) — *includes command* — `export LLM_PROVIDER=OPENAI` (command)
- Usage (section) — *includes command* — `export LLM_MODEL_NAME=gpt-4o` (command)
- Usage (section) — *includes command* — `export LLM_PROVIDER=GOOGLE_GEMINI` (command)
- Usage (section) — *includes command* — `export LLM_MODEL_NAME=gemini-2.5-flash-lite` (command)
- Usage (section) — *includes command* — `python -m uvicorn main:app --reload` (command)
- Slide 29 — *follows* — Slide 28 - Prompt Engineering for Agentic Systems
- Slide 29 — *precedes* — Slide 30 - RAG (Retrieval-Augmented Generation)
- Slide 29 — *links to* — Index

%% ai-graph-end %%