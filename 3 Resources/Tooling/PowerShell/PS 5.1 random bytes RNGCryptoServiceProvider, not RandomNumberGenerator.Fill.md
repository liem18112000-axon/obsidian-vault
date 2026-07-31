---
title: "PS 5.1 random bytes: RNGCryptoServiceProvider, not RandomNumberGenerator.Fill"
created: 2026-07-04
type: howto
status: seedling
source: "session 2026-07-04, vinnstack NEXTAUTH_SECRET"
tags: [powershell, crypto, secrets]
---

# PS 5.1 random bytes: RNGCryptoServiceProvider, not RandomNumberGenerator.Fill

Generating a cryptographic secret in Windows PowerShell 5.1 (.NET Framework): `[System.Security.Cryptography.RandomNumberGenerator]::Fill($bytes)` does NOT exist (that static is .NET Core 3+/PS 7). Use the classic provider:

    $rng = New-Object System.Security.Cryptography.RNGCryptoServiceProvider
    $bytes = New-Object byte[] 32; $rng.GetBytes($bytes)
    $secret = [Convert]::ToBase64String($bytes)   # 44 chars, fine for NEXTAUTH_SECRET

Trap observed: the method-not-found error is NON-terminating for the surrounding statement sequence - later statements in the same command still ran and wrote an EMPTY `NEXTAUTH_SECRET=` line into .env while printing the success message. After any PS 5.1 multi-statement command that had an error mid-way, verify the artifact (e.g. assert the value's length) instead of trusting the tail output.

## Related

- [[PS 5.1 mangles embedded double quotes in args to native exes]]
