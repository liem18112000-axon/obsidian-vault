---
ai_hash: bdc562b80cc872f6
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-24
entities: []
source: session 2026-06-24
status: seedling
tags:
- windows
- imagemagick
- convert
- gotcha
- cli
title: Windows 'convert' is NTFS convert.exe, not ImageMagick
type: gotcha
---

# Windows 'convert' is NTFS convert.exe, not ImageMagick

On Windows, \`command -v convert\` / \`which convert\` resolves to **\`C:\Windows\System32\convert.exe\`** — the NTFS FAT→NTFS filesystem conversion tool — **not ImageMagick**. A giveaway: running it prints \`Invalid drive specification\` instead of a version banner.

**Why it matters:** a script that detects an image tool by probing \`convert\` will get a false positive and, worse, *running it on a drive argument could attempt a filesystem conversion*. Always verify with \`convert --version\` (ImageMagick prints "Version: ImageMagick ..."), or prefer the explicit \`magick\` binary on modern ImageMagick. If you only see \`Invalid drive specification\`, ImageMagick is not installed.

%% ai-graph-start %%

**Related notes:**
- _(none above threshold)_

%% ai-graph-end %%