---
name: Message numbering convention
description: User tags each prompt with [MN]; reply must echo the same [MN] at the start. Counter persists within a conversation.
type: feedback
originSessionId: 10253868-b4d2-48d0-b16b-b401946315ec
---
**Rule:** The user prefixes each prompt with `[MN]` where N is a monotonically increasing integer (e.g. `[M1]`, `[M2]`, `[M3]`…). Every reply must begin with the same `[MN]` tag that was on the incoming prompt — nothing before it except any format requirements from other rules.

**Why:** Established 2026-04-23. The user wants to be able to reference specific exchanges ("what we said in M7") without scrolling or quoting. The tag is a shared index into the conversation; the reply echoing it is how the pairing becomes machine-parseable in logs and transcripts.

**How to apply:**
- Extract the `[MN]` from the first line of the user's message. If present, mirror it as the first line of the reply.
- If the user forgets the tag in a turn, don't invent one — reply normally and let them resume numbering next turn.
- The counter is per conversation, not per session — if a conversation resumes from a prior transcript, continue from where it left off (don't reset).
- **Composition with `reply_separator.md` (prepend `---`):** put `[MN]` on its own line FIRST, then `---` on its own line, then the reply body. The `[MN]` is the identity tag; the `---` is the visual break between tag and body. Example:

  ```
  [M2]

  ---

  (reply content here)
  ```
- If the user writes `[M2]` but I'm mid-tool-call and can't reply yet, the `[MN]` still goes at the top of my eventual text response, not interleaved with tool calls.