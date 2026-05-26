---
title: "Slide 30: RAG (Retrieval-Augmented Generation)"
slide_number: 30
tags: [lecture/slide, langgraph, agentic-programming]
source: https://github.com/trieu/ai-trip-planner/blob/main/docs/AGENTIC_PROGRAMMING_LECTURE.md
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
