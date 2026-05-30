---
ai_hash: 81accc85a1b4a503
ai_model: google/gemini-2.5-flash
ai_updated: '2026-05-30'
entities:
- RAG (Retrieval-Augmented Generation)
- LLMs (Large Language Models)
- Roppongi
- Art Triangle museums
- Trip Planner RAG Architecture
- TravelRAGService (Class)
- Chroma (Vector Store)
- OpenAIEmbeddings (Class)
- text-embedding-3-small (Model)
- load_guides (Method)
- data/local_guides.json (File)
- retrieve_recommendations (Method)
- Research Node (Component)
- _research_node (Function)
- TripState (Data Structure)
- rag_service (Instance)
- llm (Instance)
- Slide 29 - Meta-LLM (Provider Agnosticism)
- Index
- Slide 31 - Scaling & Performance Optimization
- Slide 30
slide_number: 30
source: https://github.com/trieu/ai-trip-planner/blob/main/docs/AGENTIC_PROGRAMMING_LECTURE.md
tags:
- lecture/slide
- langgraph
- agentic-programming
title: 'Slide 30: RAG (Retrieval-Augmented Generation)'
---

# Slide 30: RAG (Retrieval-Augmented Generation)

### The Problem

LLMs hallucinate facts. Grounding them with real data helps:

```
❌ Without RAG:
  LLM: "Roppongi is famous for its zen temples"
  Reality: Roppongi is an expensive nightlife district

✅ With RAG:
  LLM retrieves: "Roppongi is famous for its nightlife and Art Triangle museums"
  LLM: "Visit Roppongi's Art Triangle museums and entertainment district"
```

### Trip Planner RAG Architecture

```python
# backend/services/travel_rag_service.py
from langchain_community.vectorstores import Chroma
from langchain_openai import OpenAIEmbeddings

class TravelRAGService:
    def __init__(self):
        self.embeddings = OpenAIEmbeddings(model="text-embedding-3-small")
        self.vectordb = Chroma(embedding_function=self.embeddings)
        
        # Load local guide data
        self.load_guides("data/local_guides.json")
    
    def load_guides(self, filepath: str):
        """Load travel guides into vector DB."""
        with open(filepath) as f:
            guides = json.load(f)
        
        # Split guides into chunks and embed
        for guide in guides:
            self.vectordb.add_documents([
                Document(
                    page_content=guide["description"],
                    metadata={"city": guide["city"], "category": guide["category"]}
                )
            ])
    
    async def retrieve_recommendations(self, destination: str, interests: list):
        """Retrieve relevant guides using vector search."""
        query = f"Best {', '.join(interests)} experiences in {destination}"
        
        results = self.vectordb.similarity_search_with_score(
            query,
            k=5,  # Top 5 results
            filter={"city": destination}
        )
        
        # Format results
        recommendations = []
        for doc, score in results:
            recommendations.append({
                "text": doc.page_content,
                "relevance": score,
                "source": "local_guides"
            })
        
        return recommendations
```

### Usage in Research Node

```python
async def _research_node(self, state: TripState) -> Dict[str, Any]:
    dest = state["trip_request"]["destination"]
    interests = state["user_profile"]["current_interests"]
    
    # Try RAG first
    rag_results = await rag_service.retrieve_recommendations(dest, interests)
    
    if rag_results and rag_results[0]["relevance"] > 0.7:
        # High-quality RAG hit
        research = format_rag_results(rag_results)
    else:
        # Fallback: LLM generates insights
        research = await llm.ainvoke([
            HumanMessage(f"Generate travel insights for {dest}")
        ])
    
    return {"research": research}
```

---

← [[Slide 29 - Meta-LLM (Provider Agnosticism)|Previous]] · [[Index|🏠 Index]] · [[Slide 31 - Scaling & Performance Optimization|Next]] →

%% ai-graph-start %%

**Related notes:**
- [[Slide 31 - Scaling & Performance Optimization]]
- [[Slide 34 - Building Adaptive Agents]]
- [[Slide 11 - The Research Node (Tool Integration)]]
- [[Slide 36 - Tool Composition]]
- [[Slide 15 - The Journey Plan Node (LLM Synthesis)]]

**Relations:**
- Slide 30 — *introduces* — RAG (Retrieval-Augmented Generation)
- RAG (Retrieval-Augmented Generation) — *mitigates problem of* — LLMs (Large Language Models) hallucinating facts
- RAG (Retrieval-Augmented Generation) — *is a technique for* — LLMs (Large Language Models)
- Roppongi — *is an example location* — Roppongi
- Art Triangle museums — *are located in* — Roppongi
- Trip Planner RAG Architecture — *describes implementation of* — RAG (Retrieval-Augmented Generation)
- Trip Planner RAG Architecture — *uses* — TravelRAGService (Class)
- TravelRAGService (Class) — *utilizes* — Chroma (Vector Store)
- TravelRAGService (Class) — *utilizes* — OpenAIEmbeddings (Class)
- OpenAIEmbeddings (Class) — *employs model* — text-embedding-3-small (Model)
- TravelRAGService (Class) — *defines method* — load_guides (Method)
- load_guides (Method) — *loads data from* — data/local_guides.json (File)
- TravelRAGService (Class) — *defines method* — retrieve_recommendations (Method)
- Research Node (Component) — *implements function* — _research_node (Function)
- _research_node (Function) — *processes* — TripState (Data Structure)
- _research_node (Function) — *interacts with* — rag_service (Instance)
- _research_node (Function) — *interacts with* — llm (Instance)
- rag_service (Instance) — *is an instance of* — TravelRAGService (Class)
- Slide 30 — *follows* — Slide 29 - Meta-LLM (Provider Agnosticism)
- Slide 30 — *is part of* — Index
- Slide 30 — *precedes* — Slide 31 - Scaling & Performance Optimization

%% ai-graph-end %%