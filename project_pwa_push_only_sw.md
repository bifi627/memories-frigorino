---
name: project_pwa_push_only_sw
description: PWA stale-page-on-deploy fix — chose push-only SW over precache-hardening; PR
metadata: 
  node_type: memory
  type: project
  originSessionId: 0267eb11-4d82-4183-abf6-a4616fc0c03d
---

Users got the OLD build on first load after a deploy, new only after manual reload, with a broken window (old session 404s on a purged lazy chunk). Root cause: the Workbox **precache** SW (`skipWaiting`+`clientsClaim`) served the old cached shell and swapped controller mid-session, bypassing the correct `no-cache` on index.html.

**Decision (branch `feat/pwa-push-only-sw`, PR #78 → stage):** dropped precaching entirely rather than hardening it. The SW's only hard requirement is Firebase background push. Now: push-only `sw.ts` (no precacheAndRoute/navigation/clientsClaim, + activate cleanup purging orphaned `workbox-precache` caches), `vite.config` `registerType:"prompt"` + `injectManifest.injectionPoint:undefined`, new `common/pwa.ts` (registers SW with no refresh handler + one-shot ChunkLoadError reload backstop), `Program.cs` no-cache on sw.js, removed workbox-core/precaching/routing deps. App shell always fresh over HTTP (index.html no-cache, /assets immutable).

**Why push-only over auto-update:** user is wary of service workers ("stuck SW" horror stories) and doesn't need offline. Push-only removes the staleness/stuck failure class structurally. Trade-off: one-time transition cost (existing precache SW controls one more visit) + offline dropped.

**Why:** user explicitly asked me to push back if a simpler/safer architecture fit better. Confidence ~90%.
**How to apply:** if revisiting PWA updates, the SW is intentionally push-only — do NOT reintroduce precaching without a deliberate offline requirement. Relates to [[feedback_preserve_ux_on_persistence_lift]].
