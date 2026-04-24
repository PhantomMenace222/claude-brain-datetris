---
name: Neighboring projects on this Mac Mini
description: Sibling projects under ~/projects that may share the host with ChatA20 (port/resource conflicts)
type: reference
originSessionId: bf44965d-501b-40a7-9c9d-24fdd447dfa7
---
Under `/Users/macminiai/projects/` there are sibling projects that share this Mac Mini with ChatA20:

- `BackA20/` — backend project, active (has running Python/uvicorn on `127.0.0.1:8000` as of 2026-04-24).
- `FrontA20/` — frontend project.
- `Sammy-Brain-Backup/` — unrelated backup; not active development.

**How to apply**: when working on ChatA20, assume BackA20/FrontA20 may be running dev servers on common ports (8000, 3000, 5173). Check before binding new host ports. These projects are likely related to ChatA20 (similar naming, same author) but each has its own directory/state — don't read files across boundaries unless the user asks.
