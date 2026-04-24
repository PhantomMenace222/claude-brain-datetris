---
name: Mac Mini container runtime and port map
description: Colima (not Docker Desktop) runs the containers, and ChatA20 uses host port 8010 because BackA20 holds 8000
type: project
originSessionId: bf44965d-501b-40a7-9c9d-24fdd447dfa7
---
**Runtime**: Colima on this Mac Mini (not Docker Desktop). Started 2026-04-24 during CHAT-M2a with `colima start --cpu 2 --memory 4 --disk 40`. Docker CLI + compose plugin via Homebrew (`docker`, `docker-compose`). `~/.docker/config.json` has `cliPluginsExtraDirs` → `/opt/homebrew/lib/docker/cli-plugins` so `docker compose` resolves. Current context: `colima`.

**Why Colima over Docker Desktop**: Mac Mini is a headless prod host — no GUI needed, lower RAM, runs as a service. Matches the user's "lightweight/free" stack pattern.

**Host port map for ChatA20**:
- API: `localhost:8010` → container `:8000` (NOT 8000 — see below)
- MinIO API: `localhost:9000`
- MinIO console: `localhost:9001`
- Postgres / Redis: internal only

**Why port 8010 for API, not 8000**: another Python/uvicorn process on this Mac Mini (believed to be BackA20, running `main.py` on `127.0.0.1:8000`) already owns host port 8000. Binding ChatA20 there silently collides (Docker's port forward appeared to bind, but curl to `localhost:8000` hit BackA20's uvicorn and got 404 for our routes).

**How to apply**: before adding any new host port binding for projects on this Mac Mini, `lsof -nP -iTCP:<port> -sTCP:LISTEN` first. If ChatA20 gets a frontend later, don't pick 3000 (likely FrontA20) or 8000 — use the 8010+ range.
