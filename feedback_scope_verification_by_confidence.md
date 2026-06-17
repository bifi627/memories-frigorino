---
name: feedback_scope_verification_by_confidence
description: "Don't run the full test/docker stack per high-confidence task; scope verification and reserve the full stack for a final gate"
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 541cc763-142b-4ee5-87a8-2789ba4fcce7
---

For high-confidence tasks (mechanical edits, fully-specified small diffs, mirror-of-a-sibling changes), verify with the CHEAPEST sufficient check — a `--filter`ed test run, `npm run tsc`/`lint`, or just reading the diff. Do NOT run the whole test stack (full `dotnet test Application/Frigorino.sln`, the Testcontainers integration suite, or `docker build`) after every such task.

Run the full stack ONCE as a final verification gate at the end of the feature (this is what [[feedback_verify_with_full_tests_and_docker]] / [[feedback_verify_with_integration_tests]] describe — they're the *final-gate* recipe, not a per-task one).

**Why:** In the undo-on-delete run, most perceived waiting came from re-running expensive verification (full sln tests + Testcontainers cold starts + docker) inside multiple per-task reviewers. Those costs are irreducible wall-clock; the fix is to run them less often, not faster. The user explicitly said "don't run the whole test stack for high-confidence tasks."

**How to apply:**
- Per-task: filtered tests / tsc+lint / orchestrator self-review of ≤2-file diffs.
- Reserve full `dotnet test` sln + `docker build` for the final task.
- Don't bother with git-worktree-based parallelism for these plans — the coordination/merge overhead (e.g. two tasks both editing `Program.cs`) isn't worth it. User: "we don't have to overcomplicate with worktrees."
- For mirror/sibling tasks, collapse the two-stage review into one combined spec+quality pass.
