---
name: Reply format — separator + bracketed message tag at top and bottom
description: Every reply opens with `originSessionId: 10253868-b4d2-48d0-b16b-b401946315ec
---
` then `[MN]`, body, then `[MN]` again at the end. Mirrors the tag from the user's prompt.
type: feedback
---

**Rule (set 2026-04-24, M48):** Every reply must follow this exact shape:

```
---
[MN]

(reply body)

[MN]
```

Three things, in this order:
1. `---` on its own line as the very first line — visual separator from the prompt above
2. `[MN]` on its own line where N matches the integer the user prefixed their prompt with (e.g. `[M48]` if the user opened with `[M48] ...`)
3. The reply body
4. `[MN]` on its own line at the very end — closing tag, mirrors the opening one

A blank line between the opening `[MN]` and the body, and a blank line between the body and the closing `[MN]`, for readability.

**Why:** Established 2026-04-24 (M48) by combining and tightening two prior rules:
- The old `reply_separator.md` rule put `---` somewhere in the reply.
- The old `feedback_message_numbering.md` rule put `[MN]` once at the start, before `---`.

The combined rule (a) puts `---` FIRST so transcripts/copy-paste have a clear visual break between user prompt and reply; (b) brackets the body with `[MN]` at top AND bottom so the user can grep transcripts unambiguously to find the boundaries of any specific exchange. Both old files are superseded and removed.

**How to apply:**
- Extract `[MN]` from the FIRST line of the user's prompt. If they wrote `[M48] Block plan ...`, mirror `[M48]`.
- If the user did not include a `[MN]` tag in this turn, do NOT invent one — reply normally without the bracketed tags. Just `---` at the top still applies.
- The counter is per conversation, not per session — if a conversation resumes, continue from wherever it left off (don't reset).
- The `[MN]` tags are plain text on their own lines, not inside code fences or formatted in any other way.
- If the user is mid-tool-call when their `[MN]` prompt arrives, the brackets still go around the eventual final text response (not interleaved with tool calls).
