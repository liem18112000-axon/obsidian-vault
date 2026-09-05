---
tags: [playwright, video, automation, ffmpeg, demo]
created: 2026-08-24
---

# Recording a browser demo video with Playwright

Technique for producing a clean E2E **demo video** (not just a test) with the Playwright node lib.

- Record via context option **`recordVideo: { dir, size }`** — the whole context is captured to `.webm`, flushed on `context.close()`. Use **`launchPersistentContext`** when you also need saved cookies/2FA trust (see [[KLARA dev login automation gotchas]]).
- **Visible cursor**: inject an `addInitScript` that appends a fixed-position arrow div + expose `window.__moveCursor(x,y)` / `window.__ripple(x,y)`; before each click, move it to the element's viewport-centre box and pulse a ripple. `addInitScript` re-runs on every navigation, so it survives full-page (JSF) loads.
- **Smooth scrolling**: `requestAnimationFrame` easing loop inside `page.evaluate`, picking the tallest scrollable element.
- **Wait on text, not element visibility**: for messages inside wrapper divs that Playwright reports *hidden*, `getByText(...).waitFor({state:'visible'})` times out even though the text is on screen. Use `page.waitForFunction(() => /regex/i.test(document.body.innerText))` instead.
- **Browser build mismatch**: `playwright@1.62` expects `chromium-1234`; if only an older build is downloaded, pass `executablePath` to an installed `ms-playwright/chromium-<v>/chrome-win64/chrome.exe` (one build older works fine).
- **webm → mp4** for PPTX embedding: transcode with a full ffmpeg (imageio-ffmpeg's binary works) — `-c:v libx264 -pix_fmt yuv420p -movflags +faststart`. Embed in pptxgenjs via `addMedia({type:'video', path, cover: <base64 poster png>})`.
