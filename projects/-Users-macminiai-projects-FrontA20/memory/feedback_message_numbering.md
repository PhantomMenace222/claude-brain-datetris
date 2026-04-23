---
name: Message numbering convention
description: User prefixes every prompt with [MN]; replies must echo the same [MN] as the first token after the reply separator so the user can track which prompt each reply pairs with when pasting between sessions
type: feedback
originSessionId: 2f51a5b5-fa5a-4230-b6bc-8c1970eca761
---
**The rule:** Every user prompt starts with `[MN]` (e.g., `[M1]`, `[M15]`, `[M42]`). My reply MUST start with the **same** `[MN]`, placed after the standard reply separator (blank line + `---`).

**Example — composition order of every reply:**
```
<blank line>
---
[M15] <actual reply content starts here>
```

**Why:** The user runs multiple concurrent Claude sessions and frequently pastes outputs between them. Without the echoed `[MN]` they lose track of which response belongs to which prompt when flipping back and forth. The number is a correlation ID, nothing more.

**How to apply:**
- Match the number literally — if the incoming prompt says `[M7]`, reply starts `[M7] `, not `[M8]` or `[M007]`.
- Placement: after the `---` separator, before any other reply text. Not inside a code block, not in headers — just the first visible content.
- If a prompt lacks `[MN]` (user forgot), reply normally without inventing one; don't break the flow to ask.
- Do not use `[MN]` in subsequent reply text, file content, commit messages, or memory — it's purely a reply-prefix marker.
