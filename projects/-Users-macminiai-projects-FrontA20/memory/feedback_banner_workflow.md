---
name: Banner workflow after IPA upload
description: After IPA N is built and uploaded to Apple, the next fix branch starts with banner (N+1).1 and cleans back to (N+1) on merge to development
type: feedback
originSessionId: 2f51a5b5-fa5a-4230-b6bc-8c1970eca761
---
**The rule, crisp form (post-M128):**

The dev-cycle number IS the banner X IS the pubspec build number. They
all advance together when a new cycle's fix branch is created. Apple
acceptance is no longer the trigger.

When `fix/<N>-frontend` (or `-backend`/`-admin-panel`) is created off
`development`, the first commit on that branch lifts the banner to
`(N.1)`. Iterations on that branch increment `.1 → .2 → .3 → ...`.
Merge to `development` cleans the banner to `(N)`. The pubspec `+`
suffix should also be at `+N` by IPA build time.

**As of 2026-04-24 (post-M128), current state:**
- Cycle just shipped: **114** (banner `2026-04-24(114.3)`, IPA `+114`)
- Next fix branch (any role): **`fix/115-...`** with banner `(115.1)`
- `development` banner after that branch merges: **(115)**
- Next IPA build number: **+115**

**Why:** The banner's `(X)` segment encodes "which dev cycle this code
belongs to". Tying it to the cycle number (and the cycle to the
branch name per M107) means a runtime crash log + a branch listing +
a TestFlight build number all use the same integer to point at the
same body of work. No mental arithmetic required.

**How to apply:**
- Cycle advance trigger = creating the first `fix/<N+1>-...` branch.
  At that moment: banner becomes `(N+1).1`, pubspec should bump to
  `+(N+1)` either on the new fix branch's first commit OR on
  `development` before the next IPA build.
- Don't wait for Apple to finish processing — and don't wait for the
  current cycle's IPA to be built either. The cycle is "the work in
  flight"; the IPA is just one snapshot of it.
- Y counter resets to `.1` on every cycle advance (new X) and does
  NOT carry across cycles.
- Pre-M128 history: X used to track Apple acceptance count (101 →
  102 → 104 → 105 series), then Apple-accepted-cycle (4 from M127).
  Both retired. See [feedback_versioning.md](feedback_versioning.md)
  for the full history note and pubspec-bump rules.
