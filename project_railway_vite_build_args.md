---
name: project_railway_vite_build_args
description: Railway service variables for VITE_* build-time vars only reach the SPA bundle if also declared as ARG+ENV in the Dockerfile
metadata:
  type: project
---

Any `VITE_*` value the React SPA reads via `import.meta.env` is inlined by `vite build` at **build time**, not runtime. Railway injects service variables into a Dockerfile build **only if they are declared as `ARG`** in the Dockerfile. Adding the variable in the Railway service settings is necessary but NOT sufficient — without a matching `ARG NAME=` (promoted to `ENV NAME=$NAME` so Vite picks it up from `process.env`) in `Application/Dockerfile`'s `build_frontend` stage, the build sees `undefined`.

**Symptom of the gap:** the feature silently no-ops in deployed envs while working locally (local reads `.env.development.local`). Concrete case: `VITE_FCM_VAPID_KEY` was set as a Railway service var but had no `ARG`, so `enablePush()` short-circuited at the `!VAPID_KEY` guard — no browser permission prompt, just the refuse toast. Fixed by adding `ARG VITE_FCM_VAPID_KEY=` + ENV line alongside the existing `VITE_FARO_*` block.

**How to apply:** when adding a new `VITE_*` env var, do BOTH: (1) declare `ARG`+`ENV` in the Dockerfile build_frontend stage, (2) set the service variable in **every** Railway environment (stage AND prod — easy to set one and forget the other). See [[project_railway_hosting]].
