---
title: "Signed-token verify fails on rotation drift - pinned public key and signing private key from different keypairs"
created: 2026-07-04
type: lesson
status: seedling
source: "session 2026-07-04 fb-info-project run 28699948285"
tags: [cryptography, keypair, licensing, debugging]
---

# Signed-token verify fails on rotation drift - pinned public key and signing private key from different keypairs

A signed-token scheme can fail even when both a private signing key and a public verify key are correctly present - if they are halves of DIFFERENT keypairs (rotation drift). Symptom from a pre-sign guard: "derived <pubA> != config <pubB>", where deriving the public key from the secret private key yields pubA but the code/config pins pubB.

Cause: someone ran keygen more than once and mixed the outputs - e.g. stored the private key from run 2 in the secret but left the public key from run 1 pinned in config. Each keygen output is an inseparable pair; you must deploy the public half and store the private half FROM THE SAME RUN.

Fix = make them match: either update the pinned public key to the private key's true public half, or set the secret to the private key whose public half is already pinned. Cleanest when a key has leaked: rotate - one fresh keygen, pin its public half, store its private half, discard the old pair.

Related: [[CI token-signing workflow - verify the secret private key matches the pinned public key before signing]], [[Offline license verification - the trust anchor must be baked into the binary, never runtime-configurable]]
