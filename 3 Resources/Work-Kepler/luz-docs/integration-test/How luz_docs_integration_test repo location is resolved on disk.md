---
ai_hash: 64fe4fe336775bb8
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-11
entities: []
source: 'vinnstack session 2026-07-11: building implement-bdd-steps skill'
status: seedling
tags:
- luz-docs-integration-test
- repo-location
- tooling
title: How luz_docs_integration_test repo location is resolved on disk
type: howto
---

# How luz_docs_integration_test repo location is resolved on disk

The `~/.claude/skills/luz-docs-integration-test/run_it.sh` skill's `locate_repo()` function resolves the `luz_docs_integration_test` repo on disk by checking a fixed candidate-path list, in order:

```
$HOME/Kepler/luz_docs_integration_test
$HOME/luz_docs_integration_test
$HOME/AI/luz_docs_integration_test
$HOME/Kepler/leo-cdp-framework/luz_docs_integration_test
```

then falls back to a bounded scan (`find "$HOME" -maxdepth 4 -type d -name luz_docs_integration_test`), accepting a match only if it also contains `requirements.txt` and a `features/` directory. If nothing matches, it stops and prints a clone notice (`git clone https://bitbucket.org/axonivy-prod/luz_docs_integration_test.git ~/Kepler/luz_docs_integration_test`) rather than cloning automatically.

On dvtliem's machine this resolves to `C:\Users\dvtliem\Kepler\luz_docs_integration_test` (git remote `bitbucket.org/axonivy-prod/luz_docs_integration_test`). Any new skill/tool that needs to locate this repo should reuse this same candidate-list-then-bounded-scan pattern rather than hardcoding one path, so it keeps working across machines.

## Related

- [[3 Resources/Work-Kepler/luz-docs/integration-test/luz_docs_integration_test has its own AI-driven BDD pipeline (generate, implement, PR agents)]]

%% ai-graph-start %%

**Related notes:**
- [[luz_docs_integration_test AI pipeline branch and PR mechanics]]
- [[Luz plugin repos how skills and hooks are packaged for distribution]]
- [[luz_docs_integration_test has its own AI-driven BDD pipeline (generate, implement, PR agents)]]
- [[vinnstack BDD pipeline stops at JiraXray, never writes files into a cloned repo]]
- [[luz-skills-plugin packages skills by category directory listed in plugin.json]]

%% ai-graph-end %%