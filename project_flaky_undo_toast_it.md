---
name: project_flaky_undo_toast_it
description: "undo-delete-toast IT flake (sonner auto-dismiss race): hover-to-pin mitigation shipped but recurred; both scenarios now @ignore'd on stage (2026-06-13)"
metadata: 
  node_type: memory
  type: project
  originSessionId: 959fac25-a9ce-45ec-8ec2-3fab0f89007b
---

**FIXED 2026-06-03** (branch `fix/undo-toast-flake-it`, commit `9d0a0bb`, off `stage`, not yet merged/pushed). Both undo scenarios (inventory AND list — they share `Shared/ToastSteps.cs` "I click undo in the delete toast") were timing-flaky. Failure signature: `Timeout 30000ms exceeded while waiting for event "Response"` — i.e. the hang was on the `await responseTask` (WaitForResponse for the `/restore` POST), NOT on the click.

**Root cause (confirmed via 4-agent competing-hypotheses debug + sonner 2.0.7 source):** the sonner toast auto-dismisses after `duration:5000` (set in `useDeleteInventoryItem.ts`/`useDeleteListItem.ts` onSuccess). Under Testcontainers load the test reached the undo click during the toast's dismiss transition — the button is still hit-testable but its `pointer-events` are disabled, so the click was *silently swallowed*, the restore POST never fired, and the response wait hung the full timeout. (Ruled out: response-predicate mismatch — route is `POST .../items/{id}/restore`→200, matches exactly; React Query invalidation race — `cancelRefetch:true` + retrying assertion; stale/duplicate toast — fresh BrowserContext+Page per scenario via `ScenarioHooks`.)

**The fix:** in `ToastSteps.cs`, hover the toast (`[data-sonner-toast]`) before clicking undo — sonner pauses its auto-dismiss timer while hovered (`expanded`/`interacting` ⇒ `pauseTimer`), removing the race. Test-only; production toast duration unchanged. Verified with 9 consecutive runs (18 scenario executions, 0 failures). Aligns with [[feedback_playwright_assertions]].

**UPDATE 2026-06-13:** the hover-to-pin mitigation (b25989eb) is in `stage` but the flake recurred (re-reported as a BUGS.md item). User chose to disable rather than re-tune timing: both undo scenarios (`Slices/Lists/ListItems.feature` + `Slices/Inventories/InventoryItems.feature`) are now tagged `@ignore` (Reqnroll → generates a skipped test method, scenario kept not deleted). `ToastSteps.cs` left in place (still referenced by the ignored scenarios). BUGS.md entry removed. Verified: IT project builds clean + `@ignore` present in generated pickles.

**Run note:** in a fresh worktree the IT needs `ClientApp/build` (gitignored). For a C#-only change, copy `build/` from the main checkout and run with `FRIGORINO_SKIP_SPA_BUILD=1` instead of `npm ci`+build (see [[project_it_serves_clientapp_build]]). Requires Docker (Testcontainers). Filter the two scenarios with `--filter "DisplayName~Undo"` (NOT `Name~`).
