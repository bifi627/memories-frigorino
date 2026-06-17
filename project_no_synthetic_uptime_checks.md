---
name: project_no_synthetic_uptime_checks
description: "Frigorino deliberately does NOT use synthetic uptime monitoring (Grafana Synthetics, Healthchecks.io, UptimeRobot, cron pings to /healthz). Railway free tier sleeps the service on idle to save cost; any periodic external ping defeats the sleep, so synthetics are intentionally skipped."
metadata: 
  node_type: memory
  type: project
  originSessionId: 540ec7f4-52a3-4f01-8a51-1ba0d5b7c3e7
---

Don't propose synthetic uptime checks, scheduled `/healthz` pings, or Hangfire heartbeat-push monitors for Frigorino.

**Why:** Frigorino runs on Railway free tier, which **sleeps the container on idle** to save cost. The wake-on-first-request behaviour is acceptable for a UAT-stage app with one client (see `project_railway_hosting`). Any periodic external probe — Grafana Synthetics, Healthchecks.io, UptimeRobot, a Hangfire job posting to a heartbeat URL — keeps the service warm permanently, which:
- nullifies the cost savings the free tier exists for,
- can push the project past free-tier compute-hour ceilings.

The implicit decision is "we accept some downtime invisibility in exchange for sleep working as designed." The user explicitly closed this off during the Faro rollout (2026-05-17).

**The backend OTel exporter is itself an internal heartbeat that defeats sleep (2026-05-25).** It's not just external pings — when backend OpenTelemetry is registered, the metrics `PeriodicExportingMetricReader` (60s) plus always-on runtime instrumentation POSTs a telemetry batch to Grafana every 60s even with zero users, and that steady outbound egress resets Railway's idle timer so the container never sleeps. Resolution chosen: **leave `OpenTelemetry__OtlpHeaders` UNSET on the scale-to-zero Railway environment** (the `Program.cs` gate then skips all OTel registration → no exporter → no heartbeat). Documented in `knowledge/Observability.md` env-contract section. Consequence: an empty `OtlpHeaders` in that env is **intentional, not a misconfiguration** — do not "fix" it by setting the header. Frontend Faro stays on (browser-side, no container egress).

**How to apply:**
- When designing alerts or monitoring, do NOT include "ping /healthz every N min" or "Hangfire job heartbeat push" as an option.
- For downtime detection at this scale, rely on **passive signals**: Faro client-side errors (a spike of `fetch` failures means users are seeing the outage), Railway's own service status, occasional manual checks. If a real client SLA appears, revisit then.
- For Hangfire silent-stop detection (`ClassifyListsJob`, etc.), prefer in-process metrics emitted on each `ExecuteAsync` completion (via the OTel meter) over external probes. The "did it run recently" question becomes "what's the last value of the metric" — pulled from Mimir, no extra service warmth.
- If the user ever moves off Railway free tier (Hobby plan or higher, or a different host that doesn't sleep), this constraint disappears — propose synthetics again at that point. Cross-link [[project_railway_hosting]] for the hosting context.
