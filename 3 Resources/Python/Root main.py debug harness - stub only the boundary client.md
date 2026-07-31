---
title: "Root main.py debug harness - stub only the boundary client"
created: 2026-07-03
type: howto
status: seedling
source: "session 2026-07-03"
tags: [python, debugging, src-layout, stub, pipeline]
---

# Root main.py debug harness - stub only the boundary client

Pattern: to make a src-layout Python pipeline debuggable with plain F5, add a `src/main.py` (or root `main.py`) that (1) puts the package dir on sys.path — `sys.path.insert(0, str(Path(__file__).parent))` when the file sits inside `src/` so no editable install is needed, (2) loads `.env` explicitly with `load_dotenv(Path(__file__).parent / ".env")` (find_dotenv can crash under stdin/heredoc execution), and (3) exposes a `--stub` flag that swaps ONLY the boundary client (the API extract) for a generator-backed fake — landing, transform, mapping, and the sink stay the real code, so breakpoints anywhere in the pipeline see production behavior. Used in appsflyer-data-connector main.py where real pulls are plan-blocked ([[AppsFlyer raw-data Pull API is plan-gated - 400 subscription error]]).

## Related

- [[AppsFlyer raw-data Pull API is plan-gated - 400 subscription error]]


Refinement: for a debug entry file, prefer an editable **settings constants block** (REPORT/DAY/SINK/STUB/COUNT at the top) over argparse — the F5 workflow is edit-values-then-run, and a parser only adds ceremony you never use under a debugger.
