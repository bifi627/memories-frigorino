---
name: project_cron_batch_sync_send
description: "For cron/externally-triggered batch jobs, do the real work synchronously in-request rather than via the in-memory Channels queue (no durability guarantee)"
metadata: 
  node_type: memory
  type: project
  originSessionId: 5aaf5240-ab70-4919-b710-3ee635819aec
---

For an externally-triggered batch job (e.g. the daily `/internal/expiry-scan`), do the real work **synchronously inside the HTTP request**, not by handing it to the in-process `BackgroundTaskQueue` (Channels) + `QueuedHostedService`.

**Why:** The in-memory queue offers **no durability guarantee** — queued work is lost on container restart/redeploy, a process crash, or a transient send failure (caught + discarded). For a once-daily cron with a dedup ledger, a lost send is **unrecoverable until the next day** (the ledger already records it as done). Doing the send in-request makes the outcome observable before the ledger row is committed / the 200 returns. (Note on Railway sleep: Railway sleeps the container after ~10 min of no inbound/outbound traffic — NOT immediately after a response — so under normal operation the queue, which drains in seconds, would usually finish first; the durability gap above is the real reason, not an instant-sleep race. See [[project_railway_hosting]] / [[project_no_synthetic_uptime_checks]].)

**How to apply:** The fire-and-forget queue is fine for **request-triggered, best-effort** work where a user is actively in the app and will re-trigger (classifier / quantity extraction). For **cron/external-trigger batches** whose loss is unrecoverable, inject the worker (e.g. `INotificationSender`) and `await` it in the loop. The expiry scan uses claim-then-send: claim the ledger row (unique-index 23505 guard for concurrent fires), then `await` the send in a per-item try/catch so one failure doesn't abort the batch.
