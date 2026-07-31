---
title: "Unrestricted directory mounts for LLM tools risk multi-megabyte single-tool-call reads"
created: 2026-07-09
type: lesson
status: seedling
source: "vinnstack session 2026-07-09"
tags: [llm, cost-optimization, tool-use, gotcha]
---

# Unrestricted directory mounts for LLM tools risk multi-megabyte single-tool-call reads

When an agentic LLM tool call is granted a filesystem directory via something like `--add-dir` plus `Read`/`Grep`/`Glob`, and that directory contains generated/cached artifacts (build caches, code-graph exports, indexes), a single naive `Read` of one file in it can consume tens of thousands to millions of tokens — often far more than the rest of the entire task combined — because these generated files have no inherent size limit the way hand-written source files do.

## Why this is easy to miss

The tool grant looks innocuous ("the model MAY read X for grounding") and works fine in testing with a small repo. It silently becomes a landmine once the directory accumulates real-world artifacts, because nothing in the prompt or tool config caps *which* file gets read or *how much* of it.

## Concrete evidence (not just theoretical)

In vinnstack, an LLM grounding step is told it "MAY read Graphify graphs under `${GRAPHIFY_DIR}`" with `Read`/`Grep`/`Glob` access to that whole directory. On a real dev machine, that directory held per-repo `graph.json` files up to **51 MB**, and even the deliberately size-reduced `slim.json` variant for a modest repo was **212 KB** — which, when read with a tool that enforces a 25,000-token single-read cap, was rejected outright as **114,775 tokens**, 4.6x over the cap. So the "small" file already blows a sane read budget; the raw files are 100-250x bigger again.

## The general lesson

Before granting broad file/directory access to an agentic LLM call, check the actual on-disk size of what's in that directory in a realistic (not toy) environment — not just whether the grant "makes sense" in the abstract. If a token-efficient query mechanism already exists for that data (e.g. a purpose-built query/traversal tool instead of raw file access), point the model at that mechanism explicitly in the prompt/tool allowlist rather than leaving raw file reads as the only reachable path — an unguided model defaults to the most expensive available method.

## Related
- [[Route tool-less LLM passes to a cheaper model tier]]
