---
name: Avoid individual-file bind mounts with Colima virtiofs
description: Colima/virtiofs can serve stale content when bind-mounting a single file from host to container; mount directories instead
type: feedback
originSessionId: bf44965d-501b-40a7-9c9d-24fdd447dfa7
---
When running Docker via Colima on this Mac Mini, **do not bind-mount individual files** in docker-compose (e.g. `./backend/pytest.ini:/app/pytest.ini`). Host edits are not always visible to the container — the container can keep serving the previous file contents until the container is restarted. Directory mounts (`./backend/tests:/app/tests`) work fine.

**Why:** hit this during M2c (2026-04-24). Edited `pytest.ini` on the host; the container's `cat /app/pytest.ini` showed truncated stale content (md5 mismatch vs host). Colima uses virtiofs/sshfs to bridge host ↔ Lima VM, and small single-file mounts on that path appear to have a cache-invalidation bug on write. Cost ~15 min of debugging a "works on host but not in container" scenario.

**How to apply:**
- For files that change rarely (config like `pytest.ini`, `alembic.ini`), rely on `COPY` in the Dockerfile only. Rebuild when they change.
- For files that change often in dev, mount the containing directory instead of the file itself.
- If you must mount a single file and see staleness, `docker compose restart <service>` forces a re-read.
