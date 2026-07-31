---
ai_hash: b5dbd4fcaf786053
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-01
entities: []
source: code review 2026-07-01 (vinnstack)
status: seedling
tags:
- llm
- agents
- memory
- context-pollution
- gotcha
title: Keep error/failure text out of LLM-summarized durable memory
type: lesson
---

# Keep error/failure text out of LLM-summarized durable memory

In an agent UI that (a) shows an error/failure notice as a chat bubble and (b) periodically summarizes the conversation into a durable memory injected into every future session, the error bubble must be **flagged and excluded from the summarization payload** — otherwise raw failure text (stderr, stack traces, session ids, throttle/billing messages) becomes permanent context the agent re-reads forever.

**Why it's easy to miss:** the summarizer usually reads the *live message list*, not the persisted chat log. So merely skipping the vault write is not enough — you must also filter the in-memory messages. Do both: don't persist the raw notice, AND tag it (e.g. `error: true` on the message) so the summarize/skill-extract/follow-up passes drop it.

**General principle:** any transient, machine-generated failure detail is *diagnostic output*, not conversation content. Feeding diagnostics into a self-summarizing memory creates a slow feedback-loop pollution — the failure of one turn degrades the context of all later turns.

Surfaced fixing vinnstack's ChatProvider fallback (Finding 4): the notice flowed via `runSummary()` into `Agentic OS/Memory.md` (injected ≤8k chars every session).

Related: [[Never cache a negative fallback in the same slot as a resolved value]] (same review).

## Related

- [[Never cache a negative fallback in the same slot as a resolved value]]

%% ai-graph-start %%

**Related notes:**
- [[Classify stream failures on the server, not the client]]
- [[Vinnstack skill-usage counter missed reads past a 4MB stdout cap]]
- [[A feedback loop with only its write side wired looks like a broken feature]]
- [[State machines must catch expected-failure operations or they get stuck forever]]
- [[Secondary-write failures should fail loud when their silent version was the actual bug]]

%% ai-graph-end %%