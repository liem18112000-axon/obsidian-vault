---
tags: [klara, epost, luz-docs-import, health, business, spec]
created: 2026-08-24
---

# ePost Health documents — ZIP import business context

The business driver behind **luz-docs-import**: let health-sector senders bulk-deliver official health documents
into a Swiss citizen's **KLARA myLife** digital storage.

- **Who:** health senders (e.g. *Post Health*, clinics, practices) → KLARA myLife citizens (multilingual CH).
- **What:** advance directives (*Patientenverfügung*), vaccination records, medical documents — `documentTypes: ["HEALTH"]`.
- **Delivery unit:** one **ZIP** ("transfer.zip"). Folders inside become storage folders (e.g. `Medical documents/`, `Vaccination/`).
  Each document `X.pdf` ships a sidecar **`X.pdf.metadata.json`**. Ignored entries: `__MACOSX/`, `.DS_Store`, `Thumbs.db`.
- **Per-document metadata (`.metadata.json`):** `senderTenantId`, `senderCompanyId`, `senderName`, `documentTitle`,
  `documentTypes`, `documentReferenceDate` (YYYY-MM-DD), and `healthData` = `author` + `documentCategory` /
  `facility` / `practiceSetting`, each a **SNOMED code** with **de/fr/it/en** labels.

Ties to other work:
- Explains why the demo's Storage shows **Vaccination** + **Medical documents** folders (they come straight from the sample ZIP).
- Explains why the **first antivirus scan is on the metadata file** (not the ZIP) — see [[luz-docs-import ZIP import call chain]].

Sources: `…/luz-docs-import/business_resource/` — "ePost — ZIP document import specification" + "ePost — Health documents development handoff". Jira LUZ-158243 / LUZ-158230 (axonivy site) cover the feature but weren't accessible via the connected Atlassian MCP (only *leocdp* granted).
