---
title: "scrapling[fetchers] extra pins playwright exactly - install deps individually to keep your own pin"
created: 2026-06-05
type: lesson
status: seedling
source: "session 2026-06-05"
tags: [python, pip, scrapling, playwright, gotcha, fb-info-project]
---

# scrapling[fetchers] extra pins playwright exactly - install deps individually to keep your own pin

A bare `pip install scrapling` gives you only the parsing core — `from scrapling.fetchers import DynamicFetcher` then fails at import time with cascading `ModuleNotFoundError`s (`curl_cffi`, then `msgspec`, ...) because the browser-fetching deps live behind the `scrapling[fetchers]` extra.

But the extra carries **exact pins**: `playwright==1.59.0` and `patchright==1.59.1`. If your project pins a different playwright (fb-info-project uses 1.60.0), installing `scrapling[fetchers]` downgrades or breaks resolution.

Workaround that proved sufficient (scrapling 0.x, 2026-06): install the extra's deps individually, skipping the playwright/patchright pins. The **minimal set for `DynamicFetcher`** is just —
```
curl_cffi msgspec browserforge apify-fingerprint-datapoints
```
(`click` arrives transitively via browserforge; `anyio`/`protego` are only used by scrapling's *spider* mode, `click` directly only by its CLI — verified by importing `DynamicFetcher` and checking which candidates landed in `sys.modules`, then `pip check` for declared-dep consistency.)

`DynamicFetcher` then imports fine against the newer playwright. Inspect any package's extras and pins without leaving Python:
```python
from importlib.metadata import metadata
md = metadata('scrapling'); md.get_all('Provides-Extra'); md.get_all('Requires-Dist')
```

## Related

- [[fb-info-project duplicates FB-fragile selectors across get_locations.py and scrapling_test.py]]
