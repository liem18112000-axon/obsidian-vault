---
tags: [klara, playwright, automation, dev, testing]
created: 2026-08-24
---

# KLARA dev.klara.tech login automation gotchas

Hard-won facts for scripting a login to **https://dev.klara.tech** (Keycloak + JSF app).

- **2FA is SMS-based** ("2-Step Verification!"), but on **dev the code is the static `999999`**. Inputs: six `input[type=number]` boxes backed by a hidden `#code`; submit with the **Login** button.
- Tick **`#rememberDeviceCheckbox`** ("Don't ask again on this device for 30 days") once, using a **persistent browser profile** (`launchPersistentContext(userDataDir)`), and every later run **skips 2FA**.
- After login a **"Select profile" modal** lists profiles. The target **"Liem Doan — KLARA myLife"** is the top, pre-highlighted row; the others are *KLARA Business*. Click the **unique "Liem Doan" label precisely** — a broad container locator clicks the list centre and selects a Business profile instead. Then click **Select**.
- The **myLife** profile lands on `luz_mylife_web .../Dashboard.xhtml` (has "Import ZIP file here", "Digital Letterbox"); a **Business** profile lands on `luz_web` (Accounting/Insurances/…) — wrong app for the docs-import demo.
- The upload success screen prints **`Participant ID: <tenant>`** — for this profile it is `9388f0ab-8f8c-4401-a097-c1164c7e16e7`, confirming which Mongo tenant to clean.

See [[Recording a browser demo video with Playwright]].
