---
name: Prompt numbering scheme (M<n>chat<sub>)
description: User's milestone tag format for ChatA20 — global counter M<n> shared across all their projects, chat<sub> is the per-step suffix
type: feedback
originSessionId: bf44965d-501b-40a7-9c9d-24fdd447dfa7
---
User's prompts to ChatA20 are tagged `M<n>chat<sub>` (e.g. `M99chat1`, `M12chat3`). Reply with the same tag in `[M<n>chat<sub>]` form.

- `M<n>` is a **global** milestone counter **shared** across the user's projects: main dev (BackA20/FrontA20), admin panel, and ChatA20.
- `chat<sub>` is the per-step suffix within ChatA20 for that milestone.

Replaces the earlier `CHAT-M1`, `CHAT-M2a` prefix style (as of 2026-04-24).

**Why:** user runs multiple sibling projects under one cross-project milestone plan and wants a single unified counter — so `M<n>` means the same thing in every project's conversation, and `chat<sub>` is the chat-specific breakdown within it. Per-project numbering created ambiguity when referencing work across the fleet.

**How to apply:**
- Use the exact `M<n>` the user sent; never renumber or increment it yourself — it is controlled globally.
- The sub counter `chat<sub>` is per-ChatA20 and within a given `M<n>` can step (chat1, chat2, …). Continue whatever sub the user sends; don't reset unilaterally.
- Wrap the reply in the `--- / [MN] / body / [MN]` envelope — see `feedback_reply_format.md` (set M106, 2026-04-24).
- If the user references a milestone from another project (e.g. "as in M14back"), treat it as coming from a sibling project's conversation — do not try to read those files or assume matching state here.
