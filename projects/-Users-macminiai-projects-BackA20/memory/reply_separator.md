---
name: Reply separator formatting
description: Every reply must start with a blank line and a originSessionId: 6d6598a4-d473-4ac3-9697-347b555f996b
---
divider so the user can visually separate their messages from mine.
type: feedback
---

Start every reply with a `---` divider on its own line (preceded by a blank line, as Markdown renders it as a horizontal rule). This includes short replies, confirmations, and reports. Do not skip it for one-line answers.

**Why:** User asked for this in the 2026-04-22 session so it's easier to tell at a glance where their message ends and mine begins in the transcript.

**How to apply:** Prepend `---\n\n` to the first text block of every response. Applies to text output only, not tool-call narrations. Tool calls can still be interleaved afterwards as normal.
