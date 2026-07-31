---
title: "Windows 'convert' is NTFS convert.exe, not ImageMagick"
created: 2026-06-24
type: gotcha
status: seedling
source: "session 2026-06-24"
tags: [windows, imagemagick, convert, gotcha, cli]
---

# Windows 'convert' is NTFS convert.exe, not ImageMagick

On Windows, \`command -v convert\` / \`which convert\` resolves to **\`C:\Windows\System32\convert.exe\`** — the NTFS FAT→NTFS filesystem conversion tool — **not ImageMagick**. A giveaway: running it prints \`Invalid drive specification\` instead of a version banner.

**Why it matters:** a script that detects an image tool by probing \`convert\` will get a false positive and, worse, *running it on a drive argument could attempt a filesystem conversion*. Always verify with \`convert --version\` (ImageMagick prints "Version: ImageMagick ..."), or prefer the explicit \`magick\` binary on modern ImageMagick. If you only see \`Invalid drive specification\`, ImageMagick is not installed.
