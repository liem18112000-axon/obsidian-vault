---
ai_hash: 057c007583425710
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
entities: []
status: active
tags:
- polaris
- vinnstack
- meeting
- action-points
title: Polaris / Vinnstack action points (running log)
type: log
---

# Polaris / Vinnstack action points

Running dated log. Newest at the bottom.

## Standing

- Share knowledge base: Obsidian Vault => Git => Implemented => Integrated.
- PRD needs a **decline** action plus an iterative distillation process. Not yet built.
- Open for Mirco: how do we build on top of Polaris?

## 02/07/2026

- More editable comments on the PRD, to refine it.
- Sync all changes in the Interrogation Room.
- Future: GCP web app edition + centralized generated content + DB.
- Shared knowledge base layout: one unique folder per person; check the persona folder first; merge into the shared folder (merge logic TBD). Chat structure = Chat / Content / Misc.
- **Agreed:** save AI-generated MD (or any file) into the repo itself.
- Questions for Mirco: why no markdown summary of the code? (team MUJI built this and got rejected). Can we run the "keep an eye on files in the repo" (Granify-style) locally on our tool until Polaris ships the feature?

## 03/07/2026

- Learn the auto-create-skill feature.
- Implement per-step / per-process-flow feedback: one tab per feedback, with the `interrogate-*` skill set plus Polaris skills. *(Screenshot removed from vault.)*
- Process Flow exporter => MD file (or any format an LLM can read to implement).
- Decision needed: deploy Vinnstack to cloud?
  - **If yes:** Google Auth login + credentials onboarding wizard; Graphify JSON centralized via a technical read-only account over all repos, refreshed by CRON every X hours; Context/Chat/Obsidian stored per account; Interrogation Room storage centralized (Q&A, PRD, Stories).
  - **If no:** integrate Vinnstack into Polaris.
- UI/UX: the generated section needs to be a tab and support comment-to-regenerate. *(Screenshot removed from vault.)*

## 06/07/2026

- Business phase: add a Save button that saves everything and triggers a vault sync.

### Polaris / onboarding decisions

- Knowledge base per account: **NO**.
- On cloud, Claude Code subscription (ultracode descope) does **NOT** work; **Vertex AI does**.
- Skills are account-based => store per account in DB or Cloud Storage. Vinnstack + agent skills are centralized. **Tokens stay local only.**
- Graphify: service account with read-only on all repos, used when Vinnstack clones/pulls, produces the graph JSON into centralized storage (account-independent — everyone sees the same view). Vault from chat is account-based, to cloud storage.

## 08/07/2026

- Do **not** create a Jira ticket when the PRD is approved; push only after the Process Flow (Story) is approved.
- Mermaid diagrams: text is being cut off — must render completely.
- New: split into smaller Stories (Process Flow), driven by a free-text instruction in the prompt.
- New: delete button to remove a Process Flow.

## 21/07/2026

- Compare two approaches: (1) use the existing skill, (2) adapt the skill — feed it the current skill plus the complaint "this is too detailed, we need a more high-level design".
  - Context: an epic from Jira is broken down in multiple rounds. Round 1 uses `interrogate-business` (fine). Round 2 uses `interrogate-technical` — developers say its questions are too detailed to answer. `interrogate-technical` must return higher-level questions/suggested answers.
  - Inputs: `C:\Users\dvtliem\Kepler\vinnstack\doc\jira-LUZ-156281.md`, previous output `C:\Users\dvtliem\Kepler\vinnstack\doc\interrogation-LUZ-156281.md`.
- **Settled: `approvePrdToJira` and `splitStory` are both needed** — same skill (`prd-to-story`, which knows how to cut work into vertical slices), different contract + input:
  - `approvePrdToJira` — PRD → Stories, the *initial* decomposition. Contract `REST_SPEC_CONTRACT`, fed the whole approved PRD. This is where Stories come into existence.
  - `splitStory` — Story → Stories, a *refinement* of one already-created Story. Contract `SPLIT_CONTRACT`, fed one Story plus free-text instruction ("split by user role"). Approve can't know in advance which Story will turn out too big.
- Max 30 questions — DONE.
- Merge Thanh's skill into story-to-process-flow — half done.

## 22/07/2026

- TODO: make the technical interrogation commentable + regenerable.
- DONE: reword "Runs the `epic-to-prd` skill via Claude over the committed answers" from "~30–90s" to "up to {match-timeout} minutes".
- IN TEST: onboarding asks the user to choose the folder path (create if missing).
- IN TEST: `prd-to-story` v2.
- IN TEST: merge Thanh's skill into `prd-to-story`, horizontal style.
- TODO: merge Story→Process Flow into `prd-to-story` so the breakdown is part of Story creation.
- DONE: keep a spinner while "## Architecture & Flow" (or the relevant diagram) has no image yet.
- TODO: schedule mode for generation (Claude Routine) — thinking about 24/7.
- Plan: Friday, email out the skills for contribution. Ask Fuji whether they want a meeting.

## 23/07/2026

- AFK mode considered feasible for the next major version, 3.0.0.
- After contributing a skill to Polaris, sync it back to the local folder to use it.

## 24/07/2026

- Decide how to handle TECHNICAL-ERROR — proposal: one general technical error message.

%% ai-graph-start %%

**Related notes:**
- [[Vinnstack Polaris integration is three passive touchpoints]]
- [[vinnstack BDD pipeline stops at JiraXray, never writes files into a cloned repo]]
- [[Technical interrogation questions must pass the 2-minute tech-lead test]]
- [[Vinnstack auth providers two patterns and the rule for adding one]]
- [[Making Polaris MCP tools reachable by Vinnstack's spawned agent (discovery + allowlist)]]

%% ai-graph-end %%