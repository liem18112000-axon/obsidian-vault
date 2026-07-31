---
ai_hash: 5b4a1c672bc244eb
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-10
entities: []
source: virtual-avatar MVP implementation, 2026-07-10
status: evergreen
tags:
- gcp
- text-to-speech
- gotcha
- python
title: Google Cloud TTS timepointing (enable_time_pointing) requires the v1beta1 client
type: lesson
---

# Google Cloud TTS timepointing (enable_time_pointing) requires the v1beta1 client

Word-timing in Google Cloud Text-to-Speech — `enable_time_pointing` on `SynthesizeSpeechRequest`, the `TimepointType.SSML_MARK` enum, and the response's `timepoints` (`mark_name` / `time_seconds`) — exists **only** on `google.cloud.texttospeech_v1beta1`, not the stable `google.cloud.texttospeech`. On the stable client the field is absent from the proto entirely, so `texttospeech.SynthesizeSpeechRequest.TimepointType` raises `AttributeError: type object 'SynthesizeSpeechRequest' has no attribute 'TimepointType'`.

**Fix:** `from google.cloud import texttospeech_v1beta1 as texttospeech`. Voice selection, audio config, SSML input, and the client class name are identical — a drop-in import swap, not a rewrite.

Verified against installed `google-cloud-texttospeech==2.37.0`. Docs summaries describe timepointing generically as "on SynthesizeSpeechRequest" without flagging the v1/v1beta1 split, so for beta-introduced GCP client fields, check the installed package rather than a docs summary.

## Related

- [[met4citizen TalkingHead is a free browser-native 3D avatar library]]
- [[Virtual avatar presenter project design plan]]

%% ai-graph-start %%

**Related notes:**
- [[Google Cloud TTS from Windows fetch the token in bash, pass via env to Python]]

%% ai-graph-end %%