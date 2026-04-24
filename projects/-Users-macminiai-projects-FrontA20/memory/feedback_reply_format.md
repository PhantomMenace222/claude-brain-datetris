---
name: Reply format — separator, opening [MN], closing [MN]
description: Every reply starts with a blank line + `originSessionId: 2f51a5b5-fa5a-4230-b6bc-8c1970eca761
---
` separator, then `[MN]` matching the prompt, and ends with the same `[MN]` as the final line. Consolidates the old separator and message-numbering rules.
type: feedback
---
**The rule — composition order of every reply:**

```
<blank line>
---
[MN]

<reply body>

[MN]
```

Three pieces, applied every time:

1. **Separator** — a blank line followed by `---` on its own line at the very top, before any content.
2. **Opening [MN]** — on the line after the separator, matching the `[MN]` the user used in the incoming prompt (e.g., prompt `[M15]` → reply starts `[M15]`).
3. **Closing [MN]** — the same `[MN]` as the final line of the reply, after the body.

**Why:** User runs multiple concurrent Claude sessions and frequently pastes outputs between them. The separator makes reply boundaries instantly scannable; the opening + closing `[MN]` are correlation IDs bracketing the reply so they can identify both the start and end when reading or pasting — useful when long replies are truncated, split, or interleaved with other session transcripts.

**How to apply:**
- Every assistant turn, no exceptions — short acknowledgements, tool-only responses, end-of-turn summaries, error reports, all get the full frame.
- Match the number literally — prompt `[M7]` → reply opens `[M7]` and closes `[M7]`, not `[M8]` or `[M007]`. Do NOT invent a number if the prompt lacks one (user forgot); just skip the `[MN]` parts and keep the `---` separator.
- The opening `[MN]` goes after `---` as the first visible content — not inside a code block, not in a header, just the tag on its own line (or prefixed to the first sentence).
- The closing `[MN]` goes on its own line as the last line of the reply (after any summary, status line, or trailing prose).
- Don't use `[MN]` elsewhere in the body, file contents, commit messages, or memory files — it's purely a reply-frame marker.
