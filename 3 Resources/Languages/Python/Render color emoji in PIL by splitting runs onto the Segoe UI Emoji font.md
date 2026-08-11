---
ai_hash: 84d4bdf5eeab1fcc
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-19
entities: []
source: session 2026-06-19
status: seedling
tags:
- pil
- pillow
- emoji
- python
- rendering
title: Render color emoji in PIL by splitting runs onto the Segoe UI Emoji font
type: howto
---

# Render color emoji in PIL by splitting runs onto the Segoe UI Emoji font

Pillow (PIL) renders only the glyphs in the ONE font you pass to `draw.text`; a UI font like Segoe UI has no color emoji, so ✅🔧🧵 come out as tofu boxes. To mix text + color emoji on one line, split the string into emoji vs non-emoji runs and draw each run with the right font, advancing x by `draw.textlength(run, font)`:

```python
EMO = ImageFont.truetype('C:/Windows/Fonts/seguiemj.ttf', 27)   # color emoji
RX = re.compile('([\U0001F000-\U0001FAFF\U00002190-\U000027BF\U00002300-\U000023FF\U00002500-\U000025FF\U00002B00-\U00002BFF\U0000FE0F]+)')
for seg in RX.split(text):
    f = EMO if RX.fullmatch(seg) else TEXTFONT
    if RX.fullmatch(seg): dr.text((x,y), seg, font=EMO, embedded_color=True)
    else: dr.text((x,y), seg, font=f, fill=color)
    x += dr.textlength(seg, font=f)
```

Gotchas: emoji runs need `embedded_color=True` (Segoe UI Emoji is a color/COLR font). Include the variation-selector U+FE0F in the class so '▶️' stays one run. Plain dingbats like ✓/✔ (U+2713/2714) are matched by the dingbat range but the *text* font often lacks them — draw them via the emoji font or drop them. Used to build a faithful Telegram-chat mockup. Relates to [[Audio-reactive anime mascot overlay for narrated videos (ffmpeg)]].

## Related

- [[Audio-reactive anime mascot overlay for narrated videos (ffmpeg)]]

%% ai-graph-start %%

**Related notes:**
- [[Animate a chat-replay video in PIL cumulative reveal + bottom-anchored scroll tween]]

%% ai-graph-end %%