---
ai_hash: 9b68fd258fa3653d
ai_model: google/gemini-2.5-flash
ai_updated: '2026-05-30'
entities:
- Slide 25
- Deployment Patterns
- Local Development
- Docker Deployment
- Production Deployment
- Render
- uvicorn
- Python
- venv
- pip
- requirements.txt
- Docker
- trip-planner
- render.yaml
- trip-planner-api
- OPENAI_API_KEY
- Slide 24
- Streaming Responses
- Index
- Slide 26
- Working with Custom Tools
slide_number: 25
source: https://github.com/trieu/ai-trip-planner/blob/main/docs/AGENTIC_PROGRAMMING_LECTURE.md
tags:
- lecture/slide
- langgraph
- agentic-programming
title: 'Slide 25: Deployment Patterns'
---

# Slide 25: Deployment Patterns

### Local Development

```bash
cd backend
python -m venv venv
source venv/bin/activate
pip install -r requirements.txt

# Run with hot reload
uvicorn main:app --host 0.0.0.0 --port 8888 --reload
```

### Docker Deployment

```dockerfile
FROM python:3.11-slim

WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY backend/ .
EXPOSE 8888

CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8888"]
```

```bash
docker build -t trip-planner:latest .
docker run -p 8888:8888 --env-file .env trip-planner:latest
```

### Production Deployment (Render)

See `render.yaml` in the repo:

```yaml
services:
  - type: web
    name: trip-planner-api
    runtime: python311
    buildCommand: pip install -r requirements.txt
    startCommand: uvicorn main:app --host 0.0.0.0 --port $PORT
    envVars:
      - key: OPENAI_API_KEY
        scope: build,runtime
```

---

← [[Slide 24 - Streaming Responses|Previous]] · [[Index|🏠 Index]] · [[Slide 26 - Working with Custom Tools|Next]] →

%% ai-graph-start %%

**Related notes:**
- [[Slide 24 - Streaming Responses]]
- [[Slide 16 - Running the Graph]]
- [[Slide 22 - Integration Testing]]
- [[Appendix B - Quick Reference]]
- [[Slide 26 - Working with Custom Tools]]

**Relations:**
- Slide 25 — *covers* — Deployment Patterns
- Deployment Patterns — *includes* — Local Development
- Deployment Patterns — *includes* — Docker Deployment
- Deployment Patterns — *includes* — Production Deployment
- Local Development — *uses* — Python
- Local Development — *uses* — venv
- Local Development — *uses* — pip
- Local Development — *runs* — uvicorn
- Local Development — *requires* — requirements.txt
- Docker Deployment — *uses* — Docker
- Docker Deployment — *uses* — Python
- Docker Deployment — *uses* — pip
- Docker Deployment — *runs* — uvicorn
- Docker Deployment — *builds* — trip-planner
- Docker Deployment — *requires* — requirements.txt
- Production Deployment — *uses* — Render
- Production Deployment — *configures with* — render.yaml
- Render — *deploys* — trip-planner-api
- trip-planner-api — *uses* — Python
- trip-planner-api — *uses* — pip
- trip-planner-api — *runs* — uvicorn
- trip-planner-api — *requires* — OPENAI_API_KEY
- trip-planner-api — *requires* — requirements.txt
- Slide 25 — *follows* — Slide 24
- Slide 25 — *follows* — Streaming Responses
- Slide 25 — *precedes* — Slide 26
- Slide 25 — *precedes* — Working with Custom Tools
- Slide 25 — *links to* — Index

%% ai-graph-end %%