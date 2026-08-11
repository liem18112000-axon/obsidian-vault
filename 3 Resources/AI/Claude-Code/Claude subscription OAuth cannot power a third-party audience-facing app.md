---
ai_hash: f02e15c83683797d
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-10
entities: []
source: deep-research pass, virtual-avatar project, 2026-07-10
status: evergreen
tags:
- claude
- anthropic
- tos
- licensing
- claude-code
- agent-sdk
title: Claude subscription OAuth cannot power a third-party audience-facing app
type: lesson
---

# Claude subscription OAuth cannot power a third-party audience-facing app

Anthropic clarified in February 2026 (still enforced as of mid-2026) that a Claude.ai Free/Pro/Max/Team/Enterprise subscription's OAuth login cannot legally be used to power an application that other people besides the subscriber send requests through — even via the Claude Agent SDK.

This is stated explicitly in Anthropic's Claude Code legal/compliance docs (code.claude.com/docs/en/legal-and-compliance): "Anthropic does not permit third-party developers to offer Claude.ai login or to route requests through Free, Pro, or Max plan credentials on behalf of their users." It is backed by a broader Consumer Terms of Service clause (§3.7) banning automated/bot access to Anthropic services except via an API key or explicit permission — Claude Code itself is the sanctioned exception for the subscriber's own personal automation, not a blanket allowance for building products.

The compliant path for building something served to your own end users (customers, an app's audience, etc.) is pay-per-token API-key billing — via Anthropic Console, AWS Bedrock, or GCP Vertex AI — which falls under Anthropic's Commercial Terms of Service instead, and those explicitly permit powering products/services made available to your own end users.

Practical implication: before designing any app that assumes "I have a Claude subscription, I'll just use that as the backend," check whether anyone other than the subscriber will be sending requests through it. If yes, that's the forbidden scenario and you need API billing instead. This policy is recent and Anthropic reserves the right to tighten enforcement further without notice, so re-check the live docs page before finalizing any architecture that depends on this.

## Related

- [[Claude models are available on GCP Vertex AI Model Garden]]
- [[Claude Code's auto-mode permission classifier blocks building subscription-auth-for-third-parties even at smoke-test scale]]
- [[Virtual avatar presenter project design plan]]

%% ai-graph-start %%

**Related notes:**
- [[Claude Code's auto-mode permission classifier blocks building subscription-auth-for-third-parties even at smoke-test scale]]
- [[Anthropic has no third-party OAuth; in-app Claude login means driving the claude auth CLI]]
- [[Claude models are available on GCP Vertex AI Model Garden]]
- [[Claude Code runs on Vertex AI via three env vars with gcloud ADC]]
- [[vinnstack spawns the local claude CLI for subscription-authenticated automation]]

%% ai-graph-end %%