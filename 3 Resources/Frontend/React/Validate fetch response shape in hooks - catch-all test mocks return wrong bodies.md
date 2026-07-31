---
title: "Validate fetch response shape in hooks - catch-all test mocks return wrong bodies"
created: 2026-07-03
type: lesson
status: seedling
source: "session 2026-07-03, vinnstack usePrdComments crash"
tags: [react, hooks, testing, fetch]
---

# Validate fetch response shape in hooks - catch-all test mocks return wrong bodies

A data hook that trusts its endpoint's response shape (`if (j.ok) setState(j.items)`) can crash the ENTIRE host component tree when the response is ok-but-wrong-shape: state becomes `undefined`, the next `state.filter(...)` throws during render, and testing-library shows an empty `<body>` with "Unhandled Errors" - a confusing symptom far from the cause.

This bites specifically when you embed a new child component (with its own fetch) into an existing component whose tests stub fetch with a catch-all URL matcher: the old mock happily answers the NEW endpoint with the OLD body (`{ ok: true, interrogation }` instead of `{ ok: true, comments }`).

Rule: in hooks, guard the shape before committing to state - `if (j.ok && Array.isArray(j.comments)) setComments(j.comments)`. One cheap conjunct turns a whole-tree crash into a benign no-op, in prod and in tests alike.
