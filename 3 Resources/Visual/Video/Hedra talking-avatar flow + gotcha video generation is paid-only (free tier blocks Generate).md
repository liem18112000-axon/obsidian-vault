---
ai_hash: 21617b1f3c381b41
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-18
entities: []
source: session 2026-06-18
status: seedling
tags:
- hedra
- lip-sync
- avatar
- tts
- video
- gotcha
- playwright
title: 'Hedra talking-avatar flow + gotcha: video generation is paid-only (free tier
  blocks Generate)'
type: lesson
---

# Hedra talking-avatar flow + gotcha: video generation is paid-only (free tier blocks Generate)

Driving Hedra (hedra.com) to make a lip-synced talking-avatar clip from one image + audio.

## Flow (browser, via Playwright MCP)
- Log in (Google OAuth via WorkOS) — needs the user's own login; can't be done headlessly with just an account-less session.
- Studio → Manual tools → **Video** → click 'Select video mode' → choose **Avatar** → model becomes **Hedra Avatar**.
- 'Add start frame' (the file input is hidden behind an overlay — don't click the input; click the 'Add frame' button → menu **Upload** → then `browser_file_upload`). Same pattern for 'Add speech'.
- First avatar use triggers a **Biometric Data Consent** dialog (face/voice). It asks you confirm no other real human's biometrics — an AI-anime image + TTS voice arguably satisfies this, but it's the user's consent to click, not the agent's.
- Cost shows next to Generate, ~**7 credits/second** of audio (≈251 for 36s, ≈53 for 7.5s).

## BLOCKER: Free tier can't generate video
Even with 100 free credits and an affordable cost shown, clicking **Generate** pops **'Upgrade to unlock video generation — only available on paid plans'**. So Hedra's avatar/video render is **paid-only** (Basic $15/mo = 1,500 credits ≈ 62 videos). Free credits don't apply to video generation.

## Implication for automation
True lip-sync via Hedra needs a paid plan. Headless-automatable alternatives: a **Replicate API token** (hosted SadTalker/Wav2Lip/video-retalking, pay-per-use) — fully scriptable. Other browser tools with freer tiers: D-ID (~5 min/mo free), lipsync.video (free no-signup), HeyGen free lip-sync. Or skip lip-sync and keep an audio-reactive mascot (PIL card + ffmpeg showwaves).

Context: Claude Hooks & Skills avatar; `C:\Users\dvtliem\.claude\docs\hook-present\avatar`.

%% ai-graph-start %%

**Related notes:**
- [[Audio-reactive anime mascot overlay for narrated videos (ffmpeg)]]

%% ai-graph-end %%