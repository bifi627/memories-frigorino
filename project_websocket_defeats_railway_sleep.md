---
name: project_websocket_defeats_railway_sleep
description: "Persistent WebSocket/SignalR connections keep the Railway container awake and defeat its idle-sleep — distinct from periodic pings; prefer revision-gated polling for live-sync features."
metadata:
  node_type: memory
  type: project
  originSessionId: 21eb46f4-9dd2-4156-9c83-8eb0db792113
---

A persistent WebSocket/SignalR connection holds the Railway container awake: a single connected client prevents idle-sleep indefinitely, driving up the compute the free tier exists to save. This is a *different* mechanism from the periodic-probe case in [[project_no_synthetic_uptime_checks]] — there it is a recurring ping; here it is one long-lived connection.

**Why:** evaluated for a live-sync feature; SignalR (<1s latency) was rejected in favour of revision-gated polling because the always-open socket would defeat sleep. The user chose polling ("B, a few seconds is fine").

**How to apply:** for sync / live-update features on Railway, default to revision-gated polling (`refetchInterval` + `refetchIntervalInBackground: false`) over a persistent socket. Only reach for WebSocket/SignalR if sub-second latency is an explicit, stated requirement — and revisit if the app ever leaves a sleep-on-idle host. Related: [[project_railway_hosting]], [[project_no_synthetic_uptime_checks]], [[project_cron_batch_sync_send]].
