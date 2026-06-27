---
name: reference_pgdump_docker_pinned_image
description: "CI pg_dump/pg_restore must run from the pinned postgres:{major} Docker image, never an apt-installed client (the apt client trails the server → version-mismatch refusal)"
metadata:
  node_type: memory
  type: reference
  originSessionId: b3ca554e-edfe-44cb-a407-30cd0bb6e898
---

GH Actions DB backup/restore steps must invoke `pg_dump`/`pg_restore` from inside the `postgres:{major}` Docker image, **not** via `apt-get install postgresql-client`. The apt client version trails the server: when stage's Postgres was cut to pg18, the runner's apt pg16 client hard-refused with `pg_dump: error: aborting because of server version mismatch (server 18.4, pg_dump 16.14)`.

**Fix:** drop the apt-install step; run the dump inside the trusted image — `docker run --rm postgres:18 pg_dump …`. Pin the image major to the current Railway Postgres major; one comment + tag bump on a future pg upgrade. User confirmed "ok this approach worked".

Related: [[project_branch_workflow]] (per-env stage/prod), [[project_railway_hosting]].
