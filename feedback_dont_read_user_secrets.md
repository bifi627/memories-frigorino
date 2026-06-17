---
name: feedback_dont_read_user_secrets
description: Never read dotnet user-secrets or the secrets.json file directly — override via env vars / launch profiles instead
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 62909c56-e779-4028-84af-0b2121af64db
---

Don't try to inspect `dotnet user-secrets list`, the secrets.json file, or any path under `%APPDATA%/Microsoft/UserSecrets/`. The user keeps private cloud DB connection strings + Firebase JSON there.

**Why:** User explicitly denied the read (twice) and stated "there is a connection string configured there, please think of a way to overwrite the connection string from the setup". Secrets are private even from read-only inspection.

**How to apply:** When you need to make a local config win over user-secrets, override via env vars (`ConnectionStrings__Database=...`), command-line args (`-- --ConnectionStrings:Database=...`), or a dedicated launch profile in `Application/Frigorino.Web/Properties/launchSettings.json`. ASP.NET precedence (low→high) is appsettings → user-secrets → env vars → CLI args, so any of the latter three beat secrets without touching them.
