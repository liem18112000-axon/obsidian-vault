---
title: "met4citizen TalkingHead is a free browser-native 3D avatar library"
created: 2026-07-10
type: term
status: evergreen
source: "deep-research pass, virtual-avatar project, 2026-07-10"
tags: [avatar, three-js, tts, lip-sync, open-source]
---

# met4citizen TalkingHead is a free browser-native 3D avatar library

met4citizen/TalkingHead (github.com/met4citizen/TalkingHead) is an open-source (MIT-licensed), actively maintained browser-native 3D avatar library built on Three.js/WebGL — it renders and animates a talking 3D avatar entirely client-side, no server-side rendering or per-minute avatar API cost.

It supports Ready Player Me GLB avatars natively (plus VRoid Studio via Blender conversion, Avaturn, and MetaPerson/AvatarSDK) — notably it does **not** support VRM format directly. Lip-sync works via Oculus OVR viseme codes (aa, E, I, O, U, PP, SS, TH, CH, FF, kk, nn, RR, DD, sil), driven by word/phoneme timing data supplied by whichever TTS engine is used. It defaults to Google Cloud TTS integration, reading word timestamps from SSML `<mark>` tags (works on Standard/Wavenet/Neural2 voice types), and also supports Microsoft Azure Speech SDK (native viseme+timestamp events) or any TTS provider that exposes word timestamps, such as ElevenLabs' WebSocket API.

The same author (met4citizen) also ships a companion project, HeadTTS (github.com/met4citizen/HeadTTS): a free, local/in-browser neural TTS engine (Kokoro-based) that emits timestamps and visemes natively, runnable via WebGPU/WASM in-browser or as a local Node WebSocket/REST server — a good zero-marginal-cost fallback to Google Cloud TTS.

This is the go-to free/self-hosted choice for building a talking-avatar web app on a budget. Paid alternatives worth knowing about if higher-fidelity full-face (not just mouth) animation is needed later: Simli (~$0.05/min, sub-500ms latency, easy web SDK, $10 free signup credit) and HeyGen Streaming/Interactive Avatar API (~$0.05/sec at 720p — notably pricier than Simli).

## Related
- [[Virtual avatar presenter project design plan]]

## Related

- [[Virtual avatar presenter project design plan]]
