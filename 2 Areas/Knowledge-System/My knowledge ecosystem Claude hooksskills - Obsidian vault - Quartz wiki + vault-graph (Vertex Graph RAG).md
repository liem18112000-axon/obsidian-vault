---
title: "My knowledge ecosystem: Claude hooks/skills -> Obsidian vault -> Quartz wiki + vault-graph (Vertex Graph RAG)"
created: 2026-06-19
type: reference
status: seedling
source: "session 2026-06-19"
tags: [obsidian, quartz, vault-graph, vertex-ai, graph-rag, knowledge-management, para]
---

# My knowledge ecosystem: Claude hooks/skills -> Obsidian vault -> Quartz wiki + vault-graph (Vertex Graph RAG)

How my personal knowledge system fits together — one vault, captured by Claude, consumed by humans and AI.

## Capture (Claude Code)
Hooks (knowledge-directive, capture-reminder) keep a standing 'save what you learn' directive in front of Claude; the **obsidian-note** skill writes atomic, PARA-filed notes with [[wikilinks]] into C:\obsidian-vault. Continuity hooks + **session-handoff/session-resume** checkpoint and restore sessions. The base grows as a side-effect of normal work.

## Store
**C:\obsidian-vault** — atomic notes, PARA (Projects/Areas/Resources/Archives), [[wikilinks]], plain Markdown. Single source of truth; it's a git repo.

## Publish for humans — Quartz (C:\quartz)
Quartz v4 digital-garden SSG. Its `content/` is a **symlink to C:\obsidian-vault**, so the site IS the vault: graph view, backlinks, search, dark mode. `publish.sh` commits the vault repo, bumps the Quartz git-submodule pointer, pushes → GitHub Pages deploy at liem18112000-axon.github.io/obsidian-quartz ('Liem's Vault').

## Query by AI — vault-graph (C:\vault-graph): Graph RAG on Vertex AI
Vault attached as a git submodule at ./vault.
- **enrich.py**: embeds each note (Vertex `text-embedding-005`, cached in vault/.vault-graph/embeddings.json) + extracts entities/typed relations (Vertex `gemini-2.5-flash`) into a `%% ai-graph %%` block + frontmatter. Idempotent via a content hash over the *human* text (ai block excluded).
- **agent.py**: graph-aware RAG — retrieve by similarity, expand along [[wikilinks]] (`--hops`), answer with Gemini citing [[notes]].
- **server.py** (docker compose): `POST /ask` (graph RAG), `POST /v1/chat/completions` + `/v1/embeddings` (Vertex proxy so Obsidian AI plugins use Gemini with a static PROXY_KEY), `GET /health`. Auth = host ADC (`gcloud auth application-default login`) bind-mounted read-only via GOOGLE_APPLICATION_CREDENTIALS; `.env` has VERTEX_PROJECT=klara-nonprod, us-central1.

## Loop
Because /ask exists, Claude can query the Graph RAG back → the knowledge it captured compounds into better answers. Capture → store → publish/index → retrieve → capture.

Presentation built at C:\Users\dvtliem\.claude\docs\obsidian-present (diagram + 1-slide pptx + 01-ecosystem-explained.md).
