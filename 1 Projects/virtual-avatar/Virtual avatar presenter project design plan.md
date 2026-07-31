---
title: "Virtual avatar presenter project design plan"
created: 2026-07-10
type: concept
status: seedling
source: "session 2026-07-10"
tags: [virtual-avatar, project, design-decision]
---

# Virtual avatar presenter project design plan

The virtual-avatar project (c:\Users\dvtliem\AI\virtual-avatar) is building a web app where a 3D avatar presents a topic and answers live audience Q&A in meetings on the user's behalf, grounded in a knowledge base prepared in advance. It's inspired by Grok's "Ani" companion (grok.com/ani) in concept — an expressive, voice-first 3D avatar — but repurposed for professional presenting/Q&A rather than companionship. Grok Ani's actual underlying tech (rendering engine, lip-sync method, latency) turned out to be undocumented/proprietary on research, so there's no real technical blueprint to copy — only the aesthetic concept was verifiable.

Constraints given: cloud = GCP only; LLM = "Claude subscription only" (Claude.ai Pro/Max, not an API key). Research showed the subscription can't legally power the audience-facing half of the app (see [[Claude subscription OAuth cannot power a third-party audience-facing app]]), so the design resolves this via Claude on GCP Vertex AI Model Garden for the live app, while the actual Claude subscription still does real work: the user personally uses Claude.ai/Claude Code to author the knowledge base and presentation script before each meeting (legitimate personal use, not audience-facing).

Chosen stack: React + Three.js frontend using met4citizen's TalkingHead (free, open-source, browser-native avatar) driven by Google Cloud TTS viseme timing; Cloud Run backend; Google Cloud Speech-to-Text for audience questions; Cloud SQL + pgvector for the knowledge base (see [[Cloud SQL plus pgvector beats Vertex AI Vector Search at small RAG scale]]); Claude Sonnet via Vertex AI for grounded, citation-forcing, refuse-on-out-of-scope Q&A generation.

A full design plan (research findings, architecture, tech stack, knowledge-base pipeline, phased roadmap + cost/risk notes) was written to that project's docs/ folder (README.md + 5 numbered docs) on 2026-07-10. Any future work on this project should read that docs/ folder as the current source of truth rather than re-deriving the design, and should re-check Anthropic's live ToS page before finalizing anything, since that policy was only clarified in Feb 2026 and could tighten further.

## Related
- [[Claude subscription OAuth cannot power a third-party audience-facing app]]
- [[Claude models are available on GCP Vertex AI Model Garden]]
- [[met4citizen TalkingHead is a free browser-native 3D avatar library]]
- [[Cloud SQL plus pgvector beats Vertex AI Vector Search at small RAG scale]]

## Related

- [[Claude subscription OAuth cannot power a third-party audience-facing app]]
- [[Claude models are available on GCP Vertex AI Model Garden]]
- [[met4citizen TalkingHead is a free browser-native 3D avatar library]]
- [[Cloud SQL plus pgvector beats Vertex AI Vector Search at small RAG scale]]
