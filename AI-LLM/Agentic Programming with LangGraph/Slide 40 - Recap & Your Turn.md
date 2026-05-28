---
title: "Slide 40: Recap & Your Turn"
slide_number: 40
tags: [lecture/slide, langgraph, agentic-programming]
source: https://github.com/trieu/ai-trip-planner/blob/main/docs/AGENTIC_PROGRAMMING_LECTURE.md
---

# Slide 40: Recap & Your Turn

### What We Learned

✅ Agentic programming moves beyond single LLM calls to orchestrated workflows  
✅ LangGraph provides state management, parallelism, and observability  
✅ State is shared, immutable data passed between nodes  
✅ Nodes can be async, use tools, call LLMs, or run custom logic  
✅ Parallel execution dramatically improves latency  
✅ Error handling & fallbacks make systems resilient  
✅ Observability (tracing) is essential for debugging  
✅ RAG + tools + LLM reasoning create powerful AI systems  

### Your Challenge

**Adapt the Trip Planner for Your Domain:**

1. **Choose a domain** (PR generator? Customer support? Research assistant?)
2. **Identify your agents** (what tasks happen in parallel?)
3. **Design your state** (what data flows between agents?)
4. **Build your tools** (what APIs/databases do agents call?)
5. **Deploy & monitor** (observe performance, iterate)

### Resources

- 📚 [LangGraph Documentation](https://docs.langchain.com/oss/python/langgraph)
- 🔗 [AI Trip Planner Repo](https://github.com/trieu/ai-trip-planner)
- 🧪 [Backend Tests](backend/tests/)
- 📊 [Arize Phoenix Observability](https://phoenix.arize.com/)
- 💬 [LangChain Discord](https://discord.gg/6adMQxSpJS)

### Final Thoughts

> "Agentic programming is not about replacing humans—it's about augmenting them with 
> specialized AI workers that collaborate to solve complex problems faster, better, and 
> with full observability."

**Now go build something amazing! 🚀**

---

← [[Slide 39 - Future of Agentic Programming|Previous]] · [[Index|🏠 Index]] · [[Appendix A - Full Trip Planner Code Example|Next]] →
