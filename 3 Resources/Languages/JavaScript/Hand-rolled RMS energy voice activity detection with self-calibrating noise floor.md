---
title: "Hand-rolled RMS energy voice activity detection with self-calibrating noise floor"
created: 2026-07-11
type: technique
status: seedling
source: "virtual-avatar session 2026-07-11, static/vad.js"
tags: [javascript, web-audio-api, vad, voice-activity-detection, dsp]
---

# Hand-rolled RMS energy voice activity detection with self-calibrating noise floor

A simple, dependency-free voice activity detector (VAD) for browser JS can be built directly on the Web Audio API's `AnalyserNode`, without any ML model or library:

1. `MediaStreamAudioSourceNode` (from `getUserMedia`'s stream) -> `AnalyserNode` (`fftSize = 512` is plenty for energy detection, no need for frequency-domain analysis).
2. Poll every ~50ms via `setInterval`, computing RMS energy from `getByteTimeDomainData`: `sqrt(mean((sample-128)/128)^2)`.
3. **Self-calibrate the noise floor** with an exponential moving average of RMS energy, updated ONLY during confirmed-quiet periods (`noiseFloor = noiseFloor*(1-alpha) + level*alpha`, alpha ~0.05) — this adapts to the actual room's ambient noise instead of using one hardcoded absolute threshold that would be wrong in a noisy room vs. a silent one.
4. Trigger "speech start" only after **N consecutive frames** (not one) exceed `noiseFloor * factor` (factor ~3.5, N~4 frames ≈ 150-200ms) — the consecutive-frame requirement is what makes this robust against clicks, pops, and single-frame spikes.
5. Trigger "speech end" only after a **hangover window** (~1000ms) of continuous quiet following detected speech — this is what prevents chopping a sentence off mid-question during a natural breath or pause; a naive "instant end on first quiet frame" VAD constantly cuts people off.

This is standard, well-understood DSP — no need to reach for a VAD library/ML model for a simple "is someone talking right now" signal in a browser app. Full implementation: `static/vad.js` in the virtual-avatar project (`createVAD(stream, audioCtx, {onSpeechStart, onSpeechEnd})`), used to let an audience member interrupt an autonomously-presenting avatar by speaking, without a push-to-talk button.

Known limitation this technique does NOT solve on its own: if the same page is also playing audio out loud (e.g. an avatar's own TTS speech) through the same device's speakers, the mic can pick that up as "someone talking." Mitigating that requires either `getUserMedia`'s `echoCancellation` constraint (imperfect, hardware-dependent) or, more robustly, gating this VAD's output at the application level so its events are ignored whenever your own audio is playing.

## Related

- [[Poll a library's public boolean state flags with a grace period when there is no completion callback]]
