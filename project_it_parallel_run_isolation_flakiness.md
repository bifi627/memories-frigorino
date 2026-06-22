---
name: project_it_parallel_run_isolation_flakiness
description: An IT that fails in a full `dotnet test` run but passes 1/1 in isolation is parallel-run shared-DB-state flakiness, not a regression — re-run it alone before bisecting or reverting
metadata:
  node_type: memory
  type: project
  originSessionId: e9ca11eb-a583-4223-ac97-e6b4d9167701
---

When an integration test fails in a full-suite run but passes when run alone (`--filter "FullyQualifiedName~<title>"`), suspect parallel-run test isolation (shared Postgres Testcontainers DB state), **not** a code regression. Re-run the suspect scenario in isolation before reverting, bisecting, or treating it as a real failure.

**Why:** `CopyToListScenario` failed *"Expected 250, Actual null"* in 2 of 3 full IT runs but passed 1/1 in isolation — pre-existing flakiness unrelated to the change under test.

**How to apply:** red IT in a full run → isolate the one scenario first (filter on **title** words, not the `.feature` file name — see [[reference_reqnroll_filter_silent_skip]]); deterministic-green in isolation = flakiness, deterministic-red = real bug. Related: [[project_flaky_undo_toast_it]], [[project_it_diagnosability_500s]], [[feedback_db_tests_testcontainers]].
