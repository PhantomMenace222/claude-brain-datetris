---
name: M-number numbering scheme across projects
description: Main dev = M<n>; ChatA20 = M<n>chat<sub>; Admin panel = M<n>panel<sub>. <n> is a global counter shared across all three.
type: feedback
originSessionId: c732c6f5-6b30-49a9-969c-571f3093382c
---
Message/task numbering uses a shared global counter `<n>` across three projects, with per-project sub-counters when two+ prompts land in different projects at the same `<n>`:

- **Main dev** (this BackA20 backend + the BackA20 FE): `M<n>` — e.g. `M99`, `M100`.
- **ChatA20** (the chat project): `M<n>chat<sub>` — e.g. `M99chat1`, `M99chat2`.
- **Admin panel**: `M<n>panel<sub>` — e.g. `M99panel1`, `M99panel2`.

Sub-counters (`chat1`, `chat2`, `panel1`, `panel2`) increment within a given `<n>` when multiple prompts go to different projects in parallel. The global `<n>` advances across all projects, so `M99` (main) and `M99chat1` (chat) and `M99panel1` (panel) share the same top-level number.

**Why:** Established M99 (2026-04-24). Replaces the previous plain-`M<n>` scheme that only tracked main-dev messages; the user now coordinates work across three projects and needs unambiguous tags so cross-project references (memory, commit messages, PR titles, Slack back-references) line up.

**How to apply:** When replying to prompts tagged in these forms, echo the exact tag the user used (e.g. reply `[M99chat1]` if prompted with `[M99chat1]`). For commit messages, prefer the bare `<n>` prefix (e.g. `M99: ...`) for main-dev work so commit history stays readable; the `chat`/`panel` suffixes are primarily for message/task tracking, not git. If the user prompts here without a suffix, assume main-dev scope.
