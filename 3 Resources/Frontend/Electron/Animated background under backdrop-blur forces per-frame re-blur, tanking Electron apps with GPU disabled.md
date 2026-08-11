---
ai_hash: 30059327d0f80bf8
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-18
entities: []
source: Vinnstack session 2026-07-18
status: seedling
tags:
- electron
- performance
- backdrop-blur
- rendering
- gpu
- vinnstack
title: Animated background under backdrop-blur forces per-frame re-blur, tanking Electron
  apps with GPU disabled
type: lesson
---

# Animated background under backdrop-blur forces per-frame re-blur, tanking Electron apps with GPU disabled

A CSS `backdrop-filter: blur()` (Tailwind `backdrop-blur`) surface samples and blurs whatever is painted BEHIND it. If any element in the layer behind it is animating, the browser must re-sample and re-blur that region **every frame** — the blur result cannot be cached while its backdrop changes.

Consequence: a single continuously-animating element in the *background* layer forces EVERY backdrop-blur panel stacked above it to repaint every frame. The cost scales with (blur radius x blurred area x number of blur panels). With GPU compositing this is tolerable; with **hardware acceleration disabled** (software/SwiftShader rendering) it means the whole window repaints continuously even while idle → pervasive UI lag.

Real case (Vinnstack Electron app, 2026-07): `electron/main.js` calls `app.disableHardwareAcceleration()` (defensive, because a failing GPU process is fatal on VMs/RDP/VDI). The global `Backdrop` had three ~672px blurred blobs on an infinite `animate-floaty` translate, sitting behind ~20 `backdrop-blur` panels. Idle CPU repaint of the entire window made it "too laggy to use." Fix = make the backdrop **static** (drop the float animation); each blur panel then caches its result and idle repainting stops. Kept GPU disabled for stability.

Rule of thumb: never animate anything in the background layer beneath a backdrop-blur surface, especially when GPU accel is off. Small animations that sit ON TOP of a blur panel (e.g. an online-dot box-shadow pulse) are fine — they do not invalidate the blur behind.

Related: [[Packaged Electron+Next.js API routes must not use process.cwd() for bundled files]].

## Related

- [[Packaged Electron+Next.js API routes must not use process.cwd() for bundled files]]

%% ai-graph-start %%

**Related notes:**
- [[Electron GPU process launch failure is fatal; disable hardware acceleration to avoid it]]

%% ai-graph-end %%