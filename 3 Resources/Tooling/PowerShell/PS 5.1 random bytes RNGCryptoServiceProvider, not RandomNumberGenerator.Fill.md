---
ai_hash: c9625a4960372d7b
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-04
entities: []
source: session 2026-07-04, vinnstack NEXTAUTH_SECRET
status: seedling
tags:
- powershell
- crypto
- secrets
title: 'PS 5.1 random bytes: RNGCryptoServiceProvider, not RandomNumberGenerator.Fill'
type: howto
---

# PS 5.1 random bytes: RNGCryptoServiceProvider, not RandomNumberGenerator.Fill

Generating a cryptographic secret in Windows PowerShell 5.1 (.NET Framework): `[System.Security.Cryptography.RandomNumberGenerator]::Fill($bytes)` does NOT exist (that static is .NET Core 3+/PS 7). Use the classic provider:

    $rng = New-Object System.Security.Cryptography.RNGCryptoServiceProvider
    $bytes = New-Object byte[] 32; $rng.GetBytes($bytes)
    $secret = [Convert]::ToBase64String($bytes)   # 44 chars, fine for NEXTAUTH_SECRET

Trap observed: the method-not-found error is NON-terminating for the surrounding statement sequence - later statements in the same command still ran and wrote an EMPTY `NEXTAUTH_SECRET=` line into .env while printing the success message. After any PS 5.1 multi-statement command that had an error mid-way, verify the artifact (e.g. assert the value's length) instead of trusting the tail output.

## Related

- [[PowerShell 5.1 eats inner double-quotes passed to native exes like gcloud]]

%% ai-graph-start %%

**Related notes:**
- _(none above threshold)_

%% ai-graph-end %%