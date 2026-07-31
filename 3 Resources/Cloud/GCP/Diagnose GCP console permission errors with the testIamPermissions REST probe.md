---
title: "Diagnose GCP console permission errors with the testIamPermissions REST probe"
created: 2026-07-04
type: howto
status: seedling
source: "session 2026-07-04, vinnstack OAuth client setup"
tags: [gcp, iam, gcloud, permissions]
---

# Diagnose GCP console permission errors with the testIamPermissions REST probe

When the GCP console says "You need additional access" the actionable questions are (a) do I really lack it, (b) can I self-grant? `gcloud projects test-iam-permissions` does NOT exist as a subcommand - call the API directly:

    $token = gcloud auth print-access-token
    Invoke-RestMethod -Method POST `
      -Uri "https://cloudresourcemanager.googleapis.com/v1/projects/PROJECT:testIamPermissions" `
      -Headers @{Authorization="Bearer $token"} -ContentType "application/json" `
      -Body '{"permissions":["clientauthconfig.clients.create","resourcemanager.projects.setIamPolicy"]}'

The response echoes ONLY the permissions you hold. Include `resourcemanager.projects.setIamPolicy` in every probe: holding it means you can self-grant the missing role (`gcloud projects add-iam-policy-binding PROJECT --member=user:... --role=...`); lacking it means escalate to an admin - no amount of gcloud will help.

Related facts from the same incident: OAuth clients need `clientauthconfig.clients.*` (narrow role: roles/oauthconfig.editor); classic OAuth 2.0 web clients can only be CREATED in the console (no gcloud/API); and console permission errors often come from the PROJECT PICKER having drifted to a different project than intended - check the top bar before diagnosing IAM.
