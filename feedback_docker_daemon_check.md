---
name: feedback_docker_daemon_check
description: When a command needs Docker (Testcontainers integration tests, docker build) and the daemon isn't running, actively prompt the user to start it instead of skipping or giving up.
type: feedback
originSessionId: 9523f9cc-164f-4884-bee7-a325f61c03aa
---
When a command requires the Docker daemon (Testcontainers-backed integration tests in `Frigorino.IntegrationTests`, `docker build` for the Dockerfile sync check) and the daemon isn't running, **actively ask the user to start Docker Desktop** before proceeding. Don't silently skip the step or treat it as "environment issue, not my problem."

**Why:** User runs Docker Desktop on demand on Windows rather than leaving it running. They want a quick prompt ("Docker daemon isn't running — please start Docker Desktop and tell me when it's ready") so they can start it and have me re-run the command, instead of finishing the task with integration coverage skipped.

**How to apply:**
- Symptoms: Testcontainers throws `DotNet.Testcontainers.Guard` / `NamedPipeClientStream` connect errors, or `docker build` returns daemon-unreachable. These mean the daemon, not Docker itself, is missing.
- Action: tell the user the daemon is down, ask them to start Docker Desktop, and continue once they confirm. After they confirm, re-run the failed command.
- Don't try to start the daemon yourself (no `Start-Service`, no `dockerd` invocation) — Docker Desktop is a GUI app and the user starts it manually.
