---
name: project_railway_hosting
description: Frigorino is deployed on Railway — affects Dockerfile/runtime choices. Railway supports Docker out of the box, no init system needed, listens on $PORT.
type: project
originSessionId: 3d42a309-c3e9-4985-8a3c-0987c22be89f
---
Frigorino is hosted on Railway (PaaS).

**Why:** Affects choices around Dockerfile base image, port binding, and image size. Railway runs OCI containers as-is — foreground process, binds to `$PORT` (defaults to 8080 which matches ASP.NET Core's container default), responds to SIGTERM. No init system needed. No interactive shell-into-container is currently offered, so distroless/chiseled images are perfectly viable for production.

**How to apply:**
- For runtime images, prefer Microsoft's chiseled variants (`aspnet:<version>-noble-chiseled`) for minimal CVE surface — Railway-compatible.
- No need to pass `--port` flags or set `ASPNETCORE_URLS` for Railway (default 8080 just works).
- When debugging on Railway, rely on `railway logs` rather than shell access — shell isn't available, so don't suggest distroless-incompatible patterns like RUN-time shell scripts in the final stage.
- SDK image only matters at build time, not deployment, so it's free to be larger/with-shell.
