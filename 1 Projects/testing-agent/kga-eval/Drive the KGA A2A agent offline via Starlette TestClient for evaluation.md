---
title: "Drive the KGA A2A agent offline via Starlette TestClient for evaluation"
created: 2026-09-04
type: howto
status: seedling
source: "session 2026-09-04"
tags: [testing-agent, kga, evaluation, a2a, pytest, gotcha]
---

# Drive the KGA A2A agent offline via Starlette TestClient for evaluation

To evaluate the Knowledge-Gathering Agent (KGA) with no network, no Cloud Run, and no LLM, drive the real executor in-process through the A2A JSON-RPC stack: wrap `KnowledgeGatheringExecutor(bank=MemoryBank(FakeBucket()), client=<recorded>)` in a `DefaultRequestHandler` + `create_jsonrpc_routes` Starlette app and hit it with Starlette `TestClient` via a `message/send` payload. This runs the genuine crawl + G0–G5 fan-out but touches nothing external. Pattern lifted from `tests/test_executor_a2a.py`.

**Why this over calling `run_gather` directly:** `run_gather(ex, ctx, q, text)` returns `None` — its output is emitted through `await reply(...)` into the EventQueue as A2A `TaskUpdater` messages. Extracting the reply text from raw events is fiddly; the TestClient path returns a clean JSON-RPC response you recursively scrape for every `text` value. Use `ensure_ascii=False` / raw text (not `json.dumps`) so em-dash tier markers (e.g. "External-LLM leads —") still substring-match.

**Gotchas dug out:**
- The doc-proposed fakes `FakeRequestContext` / `CapturingEventQueue` / `run_gather_offline` / `RunTrace` do NOT exist in the repo — they were only a design spec. Build on the TestClient pattern instead.
- `RunLog` (sources/nodes_fetched/…) is **write-only markdown** in the bank — there is no `read_run_log`. To recover the pack you read `bank.load_index()[0].nodes.keys()`; to get a structured `CrawlResult.run` you must call `knowledge_gathering.loop.crawl(...)` directly (run_gather discards it).
- **Tier trace** is not a RunLog field — it is string-matched from the gather reply’s md_blocks (order G2→B1→G0→G1→G4): "Hypothesized focus:", "climbed to structural parent", "Prior knowledge from memory", "Atlassian search (seed was thin) surfaced", "External-LLM leads".
- **Refine** cannot be driven in one shot through the executor (it pauses per round in `input-required`). Use the synchronous driver `common.interrogate.loop.refine(bank, ctx, seed=..., answer_fn=accept_recommendation) -> RefineResult` to run it end-to-end offline; `.understanding` is the brief.
- A recorded Atlassian client only needs the high-level async methods (`get_issue`, `get_issue_remote_links`, optional `get_issue_dev_status`/`search_jql`/`search_cql`/`get_page`); `_dev_status` uses `getattr(client, ...)` so omitting dev-status just yields no dev links (no crash).
- Never point the harness at the prod `GCS_BUCKET` — a gather WRITES the index. Use `MemoryBank(FakeBucket())` or a tmp bank.

Related: [[Testing Agent KGA evaluation harness (ADK + RAGAS)]]

## Related

- [[Testing Agent KGA evaluation harness (ADK + RAGAS)]]
