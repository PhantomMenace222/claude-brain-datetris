---
name: Banner workflow after IPA upload
description: After IPA N is built and uploaded to Apple, the next fix branch starts with banner (N+1).1 and cleans back to (N+1) on merge to development
type: feedback
originSessionId: 2f51a5b5-fa5a-4230-b6bc-8c1970eca761
---
**The rule, crisp form:**

After IPA **N** is built + uploaded, the next fix branch starts with banner **(N+1).1** and cleans back to **(N+1)** on merge to development.

Example: IPA 104 just uploaded → next `fix/<desc>` begins at banner `2026-04-23(105.1)`; merge to `development` produces banner `2026-04-23(105)`.

**As of 2026-04-23, current state:**
- Last uploaded IPA: **104**
- Next fix branch banner: **(105.1)**
- `development` banner after that branch merges: **(105)**

**Why:** The banner's `(X)` segment encodes "which store build will carry this code". Bumping X the instant an upload happens keeps development runtimes self-labelling for the *forthcoming* release, which is what matters for diagnosing logs or TestFlight crashes.

**How to apply:**
- The moment an IPA is built with `--build-number=N` and uploaded to Apple, start treating N as "done". The very next fix branch opens at `(N+1).1`.
- Don't wait for Apple to finish processing — upload is the trigger.
- Y counter resets to `.1` on every cycle advance (new X).
- For full context on Y-carryover within a cycle, `pubspec` rules, and cross-platform unification, see [feedback_versioning.md](feedback_versioning.md).
