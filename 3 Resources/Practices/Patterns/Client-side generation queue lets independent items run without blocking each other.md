---
ai_hash: 168a9500a07f7a50
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-07
entities: []
source: Vinnstack session 2026-07-07 (components/InterrogationView.tsx, components/StoryFlowView.tsx)
status: seedling
tags:
- react
- queue
- async
- ux
- vinnstack
title: Client-side generation queue lets independent items run without blocking each
  other
type: howto
---

# Client-side generation queue lets independent items run without blocking each other

A UI that kicks off several long-running background jobs (AI generations, builds, scans) does not need real parallelism to feel non-blocking. One client-side FIFO queue is enough — **provided "in progress" is tracked per target, not as a global boolean**.

**Queue shape.** One array of pending tasks; element 0 is the running one (request already sent), the rest wait. An effect watches the array: when the head changes to a not-yet-started task it fires the real work and removes the task when it settles, which promotes the next head and re-triggers the effect. Same shape as the server-side `jobs` Map + [[Promise-chain queueTail pattern serializes async jobs with instant enqueue]], re-expressed in React state so the UI re-renders on each phase change.

**The bit that carries the UX.** Replace the global `busy` flag with a `taskStatus(kind, target)` lookup so every button asks "is MY target running or queued?". Epic B's controls stay clickable while Epic A generates; a global flag would disable the whole view.

**Correctness detail.** A finishing job must apply its result only if the user is still viewing that item — otherwise a background completion yanks the view. Keep "what am I looking at" in a ref (synced by an effect) so the completion handler reads the live value, not a stale closure.

## Related

- [[Promise-chain queueTail pattern serializes async jobs with instant enqueue]]

%% ai-graph-start %%

**Related notes:**
- [[Promise-chain queueTail pattern serializes async jobs with instant enqueue]]
- [[Non-blocking usage capture fire-and-forget async writes + a serialized promise queue for race-safe RMW]]
- [[Honest progress UI for un-streamable long LLM runs - elapsed time plus stage hints]]
- [[Parallelize independent async startup steps in an Electron main process]]

%% ai-graph-end %%