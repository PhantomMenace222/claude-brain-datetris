---
name: ChatA20 v1 scope and hosting decisions (CHAT-M2-go-FINAL, 2026-04-24)
description: Milestone-2 decisions for ChatA20 — infra-agnostic chat app, Mac Mini first, migration triggers
type: project
originSessionId: bf44965d-501b-40a7-9c9d-24fdd447dfa7
---
ChatA20 is a chat app. At CHAT-M2-go-FINAL (2026-04-24) the user locked these v1 decisions:

- **Hosting**: Mac Mini (Docker Compose) for both dev and initial prod — free.
- **Tunnel/TLS/DDoS**: Cloudflare Tunnel (free tier) to `chat.datetris.date`. No port forwarding.
- **Migration trigger**: ~1000 active users — reassess then (likely AWS, using credits).
- **Stack**: Python 3.12 + FastAPI. S3-compatible API for media (MinIO local, swappable).
- **E2E encryption**: deferred to v2. v1 schema must include an `encryption_version` hook column on message tables.
- **Push**: routed through Datetris (not a third-party direct).
- **Registry**: GHCR (free).
- **Design constraint**: infra-agnostic — Docker Compose, env vars, S3-compatible. Migration to AWS/DO/etc. should be config + ship, not a rewrite.

**Why:** user is cost-sensitive for v1 and wants no lock-in — the goal is "move = config swap, never rewrite."

**How to apply:** when proposing new deps, services, or APIs, prefer infra-neutral choices (open protocols, env-driven config, pluggable adapters). Flag any choice that would bind the app to a specific host. The `encryption_version` column must be present from whatever the first messages migration is — don't skip it with "we'll add it in v2."

Milestone shorthand (historical, pre-2026-04-24): M1 = design, M2 = infra/scaffold (M2a = compose up works, M2b = data model). Post-2026-04-24 the user switched to the `M<n>chat<sub>` global-counter scheme — see feedback_numbering_scheme.md.

**Git identity for this repo**: remote `origin = https://github.com/PhantomMenace222/ChatA20.git` (GitHub user `PhantomMenace222`, email `jasonschatalerts@gmail.com`). As of 2026-04-24 the initial push is pending user's interactive `gh auth login` — `gh` is installed but unauthenticated on this Mac Mini.
