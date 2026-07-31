---
title: "Client-side generation queue lets independent items run without blocking each other"
created: 2026-07-07
type: howto
status: seedling
source: "Vinnstack session 2026-07-07"
tags: [react, queue, async, ux, vinnstack]
---

# Client-side generation queue lets independent items run without blocking each other

A UI that lets a user kick off several long-running background jobs (AI generations, builds, scans) doesn't need real parallelism to feel non-blocking — a single client-side FIFO queue is enough, as long as "in progress" state is tracked per-target (which item/action) instead of as one global boolean.

The shape: keep one array of pending tasks, where element 0 is the one actually running (its request has been sent) and the rest are waiting. A small effect watches the array and, whenever its head changes to a task that hasn't been started yet, fires the real work (e.g. a fetch call) and removes that task from the array when it settles — which naturally promotes the next task to head and re-triggers the effect. This is the same shape as `lib/graphifyRunner.ts`'s server-side `jobs` Map + `queueTail` promise chain (see [[Promise-chain queueTail pattern serializes async jobs with instant enqueue]]), just re-implemented with React state instead of a bare module-level promise, because the UI needs to re-render on every phase change.

The part that actually matters for UX is replacing a single global `busy` boolean with a `taskStatus(kind, target)` lookup: every button asks "is MY exact target running or queued?" rather than "is anything running anywhere?". That's what lets submitting work for Epic B stay fully clickable while Epic A's generation is in flight — the old global flag would have disabled every button in the whole view, not just the one for the busy item, even though the two operations have nothing to do with each other.

One correctness detail worth calling out: when a queued job finishes, it must NOT unconditionally update whatever the user is currently looking at — only apply its result if the user is still viewing that same item, otherwise a background job completing yanks the view away from wherever the user navigated to in the meantime. Track "what am I currently looking at" in a ref (kept in sync via an effect) so the async completion handler can read the live value without a stale closure.

Implemented for Vinnstack's Interrogation Room + Process Flow generation (`components/InterrogationView.tsx`, `components/StoryFlowView.tsx`), 2026-07-07.

## Related

- [[Promise-chain queueTail pattern serializes async jobs with instant enqueue]]
