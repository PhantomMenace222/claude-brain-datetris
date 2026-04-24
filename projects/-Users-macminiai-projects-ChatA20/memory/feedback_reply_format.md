---
name: Reply envelope format (originSessionId: bf44965d-501b-40a7-9c9d-24fdd447dfa7
---
/ [MN] / body / [MN])
description: Every user-facing reply must open with ---, then [MN], body, then [MN] again — strict format set 2026-04-24
type: feedback
---

Every reply to the user in this project must follow this exact envelope:

```
---
[MN]

<body>

[MN]
```

Where `[MN]` is the **exact** milestone tag the user sent in the opening line of their prompt (e.g. `[M106chat1]`). See `feedback_numbering_scheme.md` for how `M<n>chat<sub>` is composed — this format sits on top of that scheme.

Rules:
- Line 1 is `---` (three hyphens, nothing else).
- Line 2 is `[MN]`.
- Blank line separating the opening `[MN]` from the body.
- Body (any markdown).
- Blank line separating the body from the closing `[MN]`.
- Last line is `[MN]`.

**Why:** set by the user in M106 (2026-04-24). The envelope likely makes replies easy to grep/parse when the user is juggling multiple concurrent project conversations (BackA20/FrontA20/admin/ChatA20) — consistent start/end sentinels let them scan or scroll to the right reply without reading mid-text.

**How to apply:**
- Apply to every reply in this project from M106 forward — including short acknowledgements, error reports, and follow-up questions.
- If the user's prompt lacks an `[MN]` tag (rare — meta/chat messages), use the most recent `[MN]` you saw from them, OR ask before guessing.
- Supersedes the earlier guidance in `feedback_numbering_scheme.md` that said "Open the reply with `[M<n>chat<sub>]` on its own line or at the top" — that was the pre-envelope rule.
