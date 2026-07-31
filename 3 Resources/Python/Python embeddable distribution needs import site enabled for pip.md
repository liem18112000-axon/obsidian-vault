---
title: "Python embeddable distribution needs import site enabled for pip"
created: 2026-06-17
type: lesson
status: seedling
source: "session 2026-06-17"
tags: [python, packaging, offline, windows]
---

# Python embeddable distribution needs import site enabled for pip

The Python **embeddable** distribution (the `python-X.Y.Z-embed-amd64.zip` portable build) ships with site-packages **disabled**: the `pythonXY._pth` file has `import site` commented out. Until you **uncomment `import site`** in that `._pth`, `python -m pip` won't work (and installed packages won't be importable).

Used when building **portable/offline Python bundles**: extract the embeddable zip, edit `pythonXY._pth`, bootstrap pip with `get-pip.py`, then `pip install` into it. Relocatable because you invoke modules via `python -m ...` rather than the absolute-path console-script shims.

## Related

- [[tiktoken downloads its BPE vocab on first use]]
