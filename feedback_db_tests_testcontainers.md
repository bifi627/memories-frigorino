---
name: feedback_db_tests_testcontainers
description: "DB-behavior tests use Postgres Testcontainers (IntegrationTests), never SQLite or EF InMemory."
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 7acc5c1c-6790-4ab7-9f32-ac98b12735a2
---

User: "we never do sqlite / inmemory tests, we favor testcontainer tests when touching the db in tests." Any test that exercises real database behavior goes in `Frigorino.IntegrationTests` (Reqnroll + Postgres Testcontainers). Do NOT add SQLite or EF-InMemory database tests.

**Why:** SQLite/InMemory diverge from real Postgres — collations (the `Rank` `"C"` collation), `ExecuteDeleteAsync` (relational-only; InMemory throws), and general relational query semantics. A test that passes on a fake provider can mask a real Postgres bug.

**How to apply:** `Frigorino.Test` (xUnit + FakeItEasy) is for pure unit tests — aggregate methods, slice logic. When a test needs the DB (relational queries, bulk delete, migrations, constraints), write it in `Frigorino.IntegrationTests` against Testcontainers. Some legacy `Frigorino.Test` slice tests still use EF InMemory via `TestApplicationDbContext` — don't extend that pattern for new DB-dependent coverage, and never introduce SQLite. Caught when a T8 maintenance-purge unit test added a SQLite dep (rejected → dropped, tech-debt logged for Testcontainer coverage). CLAUDE.md Testing section updated to match. Relates to [[project_it_serves_clientapp_build]], [[feedback_verify_with_integration_tests]].
