---
ai_hash: 5feb511bba38af75
ai_model: google/gemini-2.5-flash
ai_updated: '2026-05-30'
entities:
- Advanced Automation
- Ollama
- Claude
- MCP
- Obsidian
- Vault
- Markdown
- Sync Across Devices
- Publishing
- AI Integration
- iCloud
- Google Drive
- Dropbox
- Git
- Obsidian Git
- Syncthing
- Obsidian Sync
- CostBench
- Toolradar
- Checkthat
- Obsidian Publish
- Quartz
- GitHub Pages
- BuildMVPFast
- MCP server
- Claude Desktop
- Claude Code
- Cursor
- mcp-obsidian
- MarkusPfundstein
- Local REST API
- obsidian-mcp-tools
- jacksteamdev
- Raw-files server
- ChatGPT
- Windsurf
- Obsidian Claude Code MCP plugin
- LobeHub
- MCP Servers
- Smart Connections
- Copilot
- HTTP-with-SSE MCP protocol
- Streamable HTTP spec
- Claim-Check pattern
- ePost
- Phone sync
- Desktop sync
- End-to-end encryption
- Version history
- Conflict resolution
- Themes
- Graph view
- Full-text search
- Static site
- Semantic search
- Obsidian templates
- SLSA provenance attestations
- BM25 search
- WebSocket
- HTTP/SSE
- Local model
- API
- Knowledge base
- Community plugin
- Commercial use
- Obsidian AI Setup
- Luz
---

# Overview
The final stage, and the one that ties back to the Ollama and Claude/MCP work you've already done with Obsidian. Three pillars: getting your vault everywhere, getting it onto the web, and wiring AI into it. This is also where the "you own plain text files" foundation pays off — every option here works _because_ your vault is just Markdown on disk.
# Sync Across Devices
You have a spectrum from free-but-fiddly to paid-and-seamless.
**Free routes:**
- **iCloud / Google Drive / Dropbox** — point your vault folder at a synced directory. Works, but prone to conflicts if two devices edit at once.
- **Git** — the engineer's choice. Version-control your vault, push/pull manually or via the **Obsidian Git** community plugin. You get full history and diffs, which suits your workflow, but it's manual and mobile git is clunky.
- **Syncthing** — peer-to-peer, no cloud, free. Reliable but needs setup on each device.
**Paid route — Obsidian Sync:** Sync is $4/month paid annually or $5/month, with end-to-end encryption and version history. The reason to pay: free alternatives introduce sync conflicts that Obsidian Sync solves with proper conflict resolution. [CostBench](https://costbench.com/software/note-taking/obsidian/)[Toolradar](https://toolradar.com/tools/obsidian/pricing)
My steer for you: since you already live in git, **Obsidian Git is the natural free choice on desktop**, with the caveat that phone sync is where it gets painful — that's exactly the friction Obsidian Sync removes if you want phone↔desktop to "just work."
**Important update for work use:** as of February 2026, commercial use is free — the previous $50/user/year commercial license requirement has been removed. So using Obsidian for your ePost work no longer needs a paid license. Students, faculty, and nonprofit employees also get 40% off Sync and Publish. [CostBench](https://costbench.com/software/note-taking/obsidian/)[Checkthat](https://checkthat.ai/brands/obsidian/pricing)
# Publishing

For turning notes into a website (docs, a digital garden, shareable references):
- **Obsidian Publish** — official, zero-config. $8/month annually or $10/month, with themes, graph, and full-text search. Click notes to publish; it handles the rest. [CostBench](https://costbench.com/software/note-taking/obsidian/)
- **Quartz** — free, open-source, publishes an Obsidian vault as a static site. It can publish Obsidian vaults but requires technical setup. For someone comfortable with GitHub Pages / static hosting, this is the no-cost equivalent and gives you full control. [Toolradar](https://toolradar.com/tools/obsidian/pricing)
Given your stack fluency, Quartz on GitHub Pages is the obvious pick if you ever want to publish — no subscription, fully yours.
# AI Integration — The Part That Matters for You

This is the upgrade to your earlier Obsidian + Ollama + Claude/MCP setup. The landscape has matured into three distinct integration paths, and which you pick depends on how you work. [BuildMVPFast](https://www.buildmvpfast.com/blog/obsidian-claude-ai-knowledge-management-system-2026)

**Path A — MCP server (Claude reads your vault through a protocol layer).** The most popular approach: you install an MCP server that exposes your vault's files to Claude Desktop, Claude Code, or Cursor, handling search, reading, writing, and frontmatter parsing — vault access from Claude's chat without switching apps. The main options: [BuildMVPFast](https://www.buildmvpfast.com/blog/obsidian-claude-ai-knowledge-management-system-2026)

- **mcp-obsidian (MarkusPfundstein)** — the most established option, but it requires the Local REST API community plugin, which means Obsidian must be running for the MCP to work. [BuildMVPFast](https://www.buildmvpfast.com/blog/obsidian-claude-ai-knowledge-management-system-2026)
- **obsidian-mcp-tools (jacksteamdev)** — adds the good stuff: semantic search based on meaning rather than keywords, and the ability to execute Obsidian templates through AI interactions with dynamic parameters. The server is distributed as a signed executable with SLSA provenance attestations, so it's the security-conscious choice. [GitHub](https://github.com/jacksteamdev/obsidian-mcp-tools)[GitHub](https://github.com/jacksteamdev/obsidian-mcp-tools)
- **A raw-files server** — zero dependencies, no Obsidian plugins, works without Obsidian running; reads raw .md files directly, with BM25 search and safe frontmatter handling, working across Claude Desktop, Claude Code, ChatGPT, Cursor, and Windsurf. This is the cleanest if you don't want Obsidian tethered to the integration. [BuildMVPFast](https://www.buildmvpfast.com/blog/obsidian-claude-ai-knowledge-management-system-2026)

**Path B — Claude Code inside the vault.** The Obsidian Claude Code MCP plugin implements an MCP server enabling Claude Code integration, supporting both WebSocket (for Claude Code) and HTTP/SSE (for Claude Desktop), with auto-discovery so Claude Code finds your vault automatically. It defaults to port 22360, and if you run multiple vaults each needs a unique port. This fits you well given you already use Claude Code. [LobeHub](https://lobehub.com/mcp/time4wiley-obsidian-claude-code-mcp)[MCP Servers](https://mcpservers.org/servers/iansinnott/obsidian-claude-code-mcp)

**Path C — In-app AI plugins.** Community plugins like Smart Connections or Copilot that run inside Obsidian and can point at a local model (your Ollama setup) or an API. Good for inline "chat with this note" without an external client.

**My recommendation for your profile:** you've got the pieces already. Use **obsidian-mcp-tools** if you want the richest Claude Desktop experience (semantic search + template execution is genuinely powerful over a knowledge base), or the **Claude Code MCP plugin** if you'd rather drive everything from Claude Code, which you already use daily. Keep Ollama wired via an in-app plugin for offline/private queries. One caveat worth knowing: these plugins intentionally use the older HTTP-with-SSE MCP protocol because the newer Streamable HTTP spec isn't yet supported by most clients — so if a connection fails, protocol mismatch is the first thing to check. [MCP Servers](https://mcpservers.org/servers/iansinnott/obsidian-claude-code-mcp)

---

**End-of-stage checkmark:** your vault syncs to your phone without manual git gymnastics, and you can ask Claude a question that gets answered from your _own_ notes — "what did we decide about the Claim-Check pattern for Luz?" returns your actual note, not a generic answer. That's the whole system closing the loop: capture → structure → query.

%% ai-graph-start %%

**Related notes:**
- [[Roadmap]]
- [[Stage 3 — Core Plugins]]
- [[Stage 5 — Workflows & Methodology]]
- [[Stage 2 — Linking & Structure]]
- [[Stage 4 — Community Plugins]]

**Relations:**
- Advanced Automation — *involves* — Ollama
- Advanced Automation — *involves* — Claude
- Advanced Automation — *involves* — MCP
- Advanced Automation — *involves* — Obsidian
- Advanced Automation — *has_pillar* — Sync Across Devices
- Advanced Automation — *has_pillar* — Publishing
- Advanced Automation — *has_pillar* — AI Integration
- Obsidian — *manages* — Vault
- Vault — *consists_of* — Markdown
- Sync Across Devices — *has_free_option* — iCloud
- Sync Across Devices — *has_free_option* — Google Drive
- Sync Across Devices — *has_free_option* — Dropbox
- Sync Across Devices — *has_free_option* — Git
- Sync Across Devices — *has_free_option* — Syncthing
- Sync Across Devices — *has_paid_option* — Obsidian Sync
- Obsidian Git — *is_a* — Community plugin
- Obsidian Git — *uses* — Git
- Obsidian Git — *for* — Obsidian
- Obsidian Sync — *offers* — End-to-end encryption
- Obsidian Sync — *offers* — Version history
- Obsidian Sync — *offers* — Conflict resolution
- CostBench — *provides_info_on* — Obsidian Sync
- Toolradar — *provides_info_on* — Obsidian Sync
- Obsidian Git — *recommended_for* — Desktop sync
- Obsidian Sync — *solves_problem_of* — Phone sync
- Obsidian — *commercial_use_status* — free
- Checkthat — *provides_info_on* — Obsidian
- Publishing — *has_official_tool* — Obsidian Publish
- Publishing — *has_open_source_tool* — Quartz
- Obsidian Publish — *offers* — Themes
- Obsidian Publish — *offers* — Graph view
- Obsidian Publish — *offers* — Full-text search
- CostBench — *provides_info_on* — Obsidian Publish
- Quartz — *publishes* — Vault
- Quartz — *creates* — Static site
- Quartz — *can_use* — GitHub Pages
- Toolradar — *provides_info_on* — Quartz
- AI Integration — *upgrades* — Obsidian AI Setup
- BuildMVPFast — *provides_info_on* — AI Integration
- AI Integration — *has_path* — Path A
- AI Integration — *has_path* — Path B
- AI Integration — *has_path* — Path C
- Path A — *involves* — MCP server
- MCP server — *exposes_vault_to* — Claude Desktop
- MCP server — *exposes_vault_to* — Claude Code
- MCP server — *exposes_vault_to* — Cursor
- mcp-obsidian — *is_a* — MCP server
- mcp-obsidian — *developed_by* — MarkusPfundstein
- mcp-obsidian — *requires* — Local REST API
- Local REST API — *is_a* — Community plugin
- obsidian-mcp-tools — *is_a* — MCP server
- obsidian-mcp-tools — *developed_by* — jacksteamdev
- obsidian-mcp-tools — *adds_feature* — Semantic search
- obsidian-mcp-tools — *adds_feature* — Obsidian templates
- obsidian-mcp-tools — *has_security_feature* — SLSA provenance attestations
- Raw-files server — *is_a* — MCP server
- Raw-files server — *reads* — Markdown
- Raw-files server — *works_with* — Claude Desktop
- Raw-files server — *works_with* — Claude Code
- Raw-files server — *works_with* — ChatGPT
- Raw-files server — *works_with* — Cursor
- Raw-files server — *works_with* — Windsurf
- Path B — *involves* — Obsidian Claude Code MCP plugin
- Obsidian Claude Code MCP plugin — *implements* — Path B
- Obsidian Claude Code MCP plugin — *enables* — Claude Code integration
- Obsidian Claude Code MCP plugin — *supports* — WebSocket
- Obsidian Claude Code MCP plugin — *supports* — HTTP/SSE
- LobeHub — *provides_info_on* — Obsidian Claude Code MCP plugin
- MCP Servers — *provides_info_on* — Obsidian Claude Code MCP plugin
- Path C — *involves* — In-app AI plugins
- Smart Connections — *is_an* — In-app AI plugin
- Copilot — *is_an* — In-app AI plugin
- In-app AI plugins — *can_use* — Local model
- In-app AI plugins — *can_use* — API
- obsidian-mcp-tools — *recommended_for* — Claude Desktop
- Obsidian Claude Code MCP plugin — *recommended_for* — Claude Code
- Ollama — *recommended_for* — Offline queries
- Ollama — *recommended_for* — Private queries
- mcp-obsidian — *uses_protocol* — HTTP-with-SSE MCP protocol
- obsidian-mcp-tools — *uses_protocol* — HTTP-with-SSE MCP protocol
- Raw-files server — *uses_protocol* — HTTP-with-SSE MCP protocol
- Obsidian Claude Code MCP plugin — *uses_protocol* — HTTP-with-SSE MCP protocol
- Streamable HTTP spec — *is_newer_than* — HTTP-with-SSE MCP protocol
- Claude — *answers_questions_from* — Knowledge base
- Knowledge base — *consists_of* — Vault
- Obsidian — *used_for* — ePost
- Vault — *syncs_to* — Phone sync
- Claude — *queries* — Vault
- Vault — *contains_concept* — Claim-Check pattern
- Vault — *mentions_person* — Luz

%% ai-graph-end %%