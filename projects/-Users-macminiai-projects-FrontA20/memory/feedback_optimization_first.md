---
name: Optimization-first by default
description: When offering solutions, lead with the most-optimized route as the default recommendation; surface tradeoffs for easier alternatives but don't default to them
type: feedback
originSessionId: 2f51a5b5-fa5a-4230-b6bc-8c1970eca761
---
**The rule:** When offering solutions or laying out options, the default recommendation is the **most-optimized route** — performance, scalability, simplicity-of-steady-state, total cost, correctness — not the most-easily-implemented one. Tradeoffs stay visible (user still sees the easier option with its cost/benefit) but efficient-by-default is the new baseline.

**Why:** User originally directed this at the PM / Claude.ai chat session when scoping work across their multi-session setup, but the preference generalizes. They don't want to pay long-term for short-term convenience; picking the quickest path often costs more over time (rework, performance debt, complexity creep). Leading with the efficient choice lets the user consciously opt *into* an easier shortcut if the moment calls for it, rather than having to ask "is there a better way" every time.

**How to apply:**
- When presenting A/B/C options, put the optimized one at the top of the list as the default, not alphabetical or time-of-implementation order.
- State the rationale in one line: "Option A (recommended): fastest at scale because X. Option B: ships sooner but rebuilds next time you need Y."
- Don't hide the easy option — user may still pick it when pressed for time. Just don't let it be the implicit default.
- "Optimized" is context-sensitive: for a prod hot path, optimize for latency/throughput; for a dev tool, optimize for clarity; for a one-off migration, optimize for reversibility. Read the situation.
- This changes framing, not execution. If the user says "just do the easy one", do it — the rule is about the *default recommendation*, not about overriding explicit requests.
