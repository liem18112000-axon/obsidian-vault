---
title: "Slide 25: Deployment Patterns"
slide_number: 25
tags: [lecture/slide, langgraph, agentic-programming]
source: https://github.com/trieu/ai-trip-planner/blob/main/docs/AGENTIC_PROGRAMMING_LECTURE.md
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
