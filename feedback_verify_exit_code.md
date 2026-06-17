---
name: feedback_verify_exit_code
description: When a piped command's exit status matters (esp. run_in_background), the pipeline reports the LAST stage's code — capture ${PIPESTATUS[0]} or read pass/fail lines, don't trust it
metadata:
  node_type: memory
  type: feedback
  originSessionId: feeeb742-c065-424d-9e72-ade9999aa069
---

When you pipe a command whose exit status you care about (e.g. `dotnet test ... | tail -60`, `npm ... | tail`), the shell reports the exit code of the LAST stage (`tail`), not the command you care about. This is especially dangerous with `run_in_background`, because the completion notification's "exit code N" is that masked pipeline code.

**Why:** In the composer-input-redesign session I ran `dotnet test Application/Frigorino.sln | tail -60` in the background. The completion notification said "exit code 0", but the suite actually had 1 failing test (`Fehler: 1, erfolgreich: 61`) — `tail` exited 0 and swallowed the real result. I nearly treated a red suite as green; I only caught it by reading the output file. On the rerun I added `; echo "EXIT=${PIPESTATUS[0]}"` and it worked.

**How to apply:** Any time you pipe a test/build/lint command and the exit code matters, append `; echo "EXIT=${PIPESTATUS[0]}"` (bash) from the FIRST run, or don't pipe at all. When relying on a `run_in_background` completion notification's exit code, remember it's the pipeline's last-stage code — confirm pass/fail by reading the summary line in the output, not the notification's number. Ties into [[feedback_verify_with_full_tests_and_docker]] and [[feedback_scope_verification_by_confidence]].
