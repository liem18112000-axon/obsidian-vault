---
tags: [claude-code, headless, debugging, mcp, vinnstack]
---

# Headless claude exit 0 does not mean the operation succeeded

A spawned `claude -p ... --output-format json` process exiting with **code 0** only means the CLI ran — not that the task worked. The model can finish "cleanly" while having done nothing useful (e.g. an MCP connector wasn't available, so it never created the Jira issues it was asked to).

So don't trust the exit code as the success signal. Log:
- the **result envelope** fields: `is_error`, `subtype`, `num_turns` (parse the JSON output, not just stdout length);
- the **parse outcome** of the model's final text (did the expected JSON extract, or is it prose like "I don't have access to Atlassian tools"?);
- the **verification** outcome (for write-backs, re-read the claimed artifacts from the source of truth — e.g. Jira REST — and log which verified vs not).

**Vinnstack case:** `approvePrdToJira` spawned claude (exit 0, ~4 min) but `POST /api/interrogation/generate` returned 400. Two distinct failure modes had collapsed into one opaque error: (a) no parseable `{commented, stories}` JSON, vs (b) it ran but nothing verified in Jira. Splitting them + logging head/tail of the result made the real cause diagnosable. The write path depends on the **claude.ai Atlassian MCP connector being authenticated in the headless run**, and verification depends on **Jira REST creds (jiraEmail/jiraApiToken)** being configured — either missing looks like "success then 400".

Related: [[Claude Code runs on Vertex AI via three env vars with gcloud ADC]].
