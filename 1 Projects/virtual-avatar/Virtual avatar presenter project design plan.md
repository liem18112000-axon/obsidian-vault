---
ai_hash: 59042f4eff7f7a0d
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-10
entities:
- Virtual avatar presenter project
- Web app
- 3D avatar
- Topic
- Audience Q&A
- Meetings
- User
- Knowledge base
- Grok
- Grok's Ani
- Companion
- Professional presenting/Q&A
- Underlying tech
- Rendering engine
- Lip-sync method
- Latency
- GCP
- LLM
- Claude subscription
- Claude.ai Pro/Max
- API key
- Audience-facing app
- Design
- Claude on GCP Vertex AI Model Garden
- Live app
- Claude.ai/Claude Code
- Presentation script
- React
- Three.js
- Frontend
- met4citizen's TalkingHead
- Google Cloud TTS viseme timing
- Cloud Run
- Backend
- Google Cloud Speech-to-Text
- Cloud SQL
- pgvector
- Vertex AI Vector Search
- Small RAG scale
- Claude Sonnet
- Vertex AI
- Q&A generation
- Design plan
- Research findings
- Architecture
- Tech stack
- Knowledge-base pipeline
- Phased roadmap
- Cost/risk notes
- docs/ folder
- README.md
- Numbered docs
- Future work
- Current source of truth
- Anthropic's ToS page
- Policy
source: session 2026-07-10
status: seedling
tags:
- virtual-avatar
- project
- design-decision
title: Virtual avatar presenter project design plan
type: concept
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

%% ai-graph-start %%

**Related notes:**
- [[Claude models are available on GCP Vertex AI Model Garden]]
- [[met4citizen TalkingHead is a free browser-native 3D avatar library]]
- [[concept-to-video skill turns a concept into deck, voiceover and narrated avatar video]]
- [[Claude Code's auto-mode permission classifier blocks building subscription-auth-for-third-parties even at smoke-test scale]]
- [[Claude subscription OAuth cannot power a third-party audience-facing app]]

**Relations:**
- Virtual avatar presenter project — *builds* — Web app
- Web app — *features* — 3D avatar
- 3D avatar — *presents* — Topic
- 3D avatar — *answers* — Audience Q&A
- 3D avatar — *operates_in* — Meetings
- 3D avatar — *operates_on_behalf_of* — User
- 3D avatar — *grounded_in* — Knowledge base
- Knowledge base — *prepared_in_advance* — true
- Virtual avatar presenter project — *inspired_by* — Grok's Ani
- Grok's Ani — *is_a* — 3D avatar
- Grok's Ani — *is_a* — Companion
- Grok's Ani — *is* — expressive
- Grok's Ani — *is* — voice-first
- Virtual avatar presenter project — *repurposed_for* — Professional presenting/Q&A
- Grok's Ani — *has* — Underlying tech
- Underlying tech — *includes* — Rendering engine
- Underlying tech — *includes* — Lip-sync method
- Underlying tech — *includes* — Latency
- Underlying tech — *is* — undocumented
- Underlying tech — *is* — proprietary
- Virtual avatar presenter project — *constraint_is_cloud* — GCP
- Virtual avatar presenter project — *constraint_is_LLM* — Claude subscription
- Claude subscription — *is_a* — Claude.ai Pro/Max
- Claude subscription — *is_not* — API key
- Claude subscription — *cannot_power* — Audience-facing app
- Design — *uses* — Claude on GCP Vertex AI Model Garden
- Claude on GCP Vertex AI Model Garden — *is_for* — Live app
- User — *uses* — Claude.ai/Claude Code
- Claude.ai/Claude Code — *to_author* — Knowledge base
- Claude.ai/Claude Code — *to_author* — Presentation script
- Claude.ai/Claude Code — *is_for* — personal use
- Virtual avatar presenter project — *uses* — React
- React — *for* — Frontend
- Virtual avatar presenter project — *uses* — Three.js
- Three.js — *for* — Frontend
- Frontend — *uses* — met4citizen's TalkingHead
- met4citizen's TalkingHead — *is* — free
- met4citizen's TalkingHead — *is* — open-source
- met4citizen's TalkingHead — *is* — browser-native avatar
- met4citizen's TalkingHead — *driven_by* — Google Cloud TTS viseme timing
- Virtual avatar presenter project — *uses* — Cloud Run
- Cloud Run — *for* — Backend
- Virtual avatar presenter project — *uses* — Google Cloud Speech-to-Text
- Google Cloud Speech-to-Text — *for* — Audience Q&A
- Virtual avatar presenter project — *uses* — Cloud SQL
- Cloud SQL — *for* — Knowledge base
- Virtual avatar presenter project — *uses* — pgvector
- pgvector — *for* — Knowledge base
- Cloud SQL plus pgvector — *beats* — Vertex AI Vector Search
- Cloud SQL plus pgvector — *at* — Small RAG scale
- Virtual avatar presenter project — *uses* — Claude Sonnet
- Claude Sonnet — *via* — Vertex AI
- Claude Sonnet — *for* — Q&A generation
- Q&A generation — *is* — grounded
- Q&A generation — *is* — citation-forcing
- Q&A generation — *refuses_on* — out-of-scope
- Design plan — *contains* — Research findings
- Design plan — *contains* — Architecture
- Design plan — *contains* — Tech stack
- Design plan — *contains* — Knowledge-base pipeline
- Design plan — *contains* — Phased roadmap
- Design plan — *contains* — Cost/risk notes
- Design plan — *written_to* — docs/ folder
- docs/ folder — *contains* — README.md
- docs/ folder — *contains* — Numbered docs
- Future work — *should_read* — docs/ folder
- docs/ folder — *is* — Current source of truth
- Future work — *should_recheck* — Anthropic's ToS page
- Anthropic's ToS page — *contains* — Policy
- Policy — *clarified_in* — Feb 2026

%% ai-graph-end %%