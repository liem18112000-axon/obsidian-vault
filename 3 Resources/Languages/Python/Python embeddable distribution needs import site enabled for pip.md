---
ai_hash: 9a24389b28d33720
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-17
entities: []
source: session 2026-06-17
status: seedling
tags:
- python
- packaging
- offline
- windows
title: Python embeddable distribution needs import site enabled for pip
type: lesson
---

# Python embeddable distribution needs import site enabled for pip

The Python **embeddable** distribution (the `python-X.Y.Z-embed-amd64.zip` portable build) ships with site-packages **disabled**: the `pythonXY._pth` file has `import site` commented out. Until you **uncomment `import site`** in that `._pth`, `python -m pip` won't work (and installed packages won't be importable).

Used when building **portable/offline Python bundles**: extract the embeddable zip, edit `pythonXY._pth`, bootstrap pip with `get-pip.py`, then `pip install` into it. Relocatable because you invoke modules via `python -m ...` rather than the absolute-path console-script shims.

## Related

- [[tiktoken downloads its BPE vocab on first use]]

%% ai-graph-start %%

**Related notes:**
- [[tiktoken downloads its BPE vocab on first use]]
- [[pip install does not bundle templatesstatic referenced relative to a package]]

%% ai-graph-end %%