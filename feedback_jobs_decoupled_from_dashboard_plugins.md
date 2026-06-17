---
name: feedback_jobs_decoupled_from_dashboard_plugins
description: "Background jobs/services must depend only on Microsoft ILogger, never on dashboard/observability plugins (e.g. Hangfire.Console / PerformContext). If logs must show in such a UI, bridge it in the composition root."
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 9c3aa10c-1944-47a3-8617-5a88d1ca1d96
---

Background jobs and application services must depend only on
`Microsoft.Extensions.Logging.ILogger<T>` for logging. They must **not** reference
dashboard/observability plugin types in their bodies or signatures — e.g. no
`PerformContext` parameter and no `context.WriteLine(...)` from Hangfire.Console.

If log output needs to surface in such a UI (e.g. the Hangfire dashboard's per-job console),
wire a **bridge in the composition root**: a custom `ILoggerProvider` (or equivalent) that
mirrors `ILogger` entries into the plugin during execution, leaving job/service code
plugin-free. The plugin dependency stays confined to one host-wiring file.

**Why:** The user uses Microsoft `ILogger` uniformly across the code (it already flows to
stdout and to OTel → Grafana/Loki) and wants job/service logic kept independent from
presentation/observability plugins. Coupling a job to a dashboard plugin is the kind of
unnecessary coupling they avoid. Confirmed 2026-05-25 while designing the Hangfire wiring:
the user rejected `context.WriteLine`-style logging because "jobs/services should be
independent from Hangfire.Console — I just use Microsoft ILogger."

**How to apply:** When integrating any job-visibility / observability plugin, push the
integration into DI/host wiring; keep job + service signatures on `ILogger<T>` only; and
confirm the job's unit test can call it with no plugin context active (the bridge must no-op
outside a real run). Note: `ILogger`/stdout is NOT auto-captured by Hangfire.Console — it
only renders lines explicitly pushed into its per-job storage buffer, which is exactly what
the composition-root bridge does.

Related: [[feedback_clean_domain_separation]], [[feedback_vendor_agnostic_by_default]].
