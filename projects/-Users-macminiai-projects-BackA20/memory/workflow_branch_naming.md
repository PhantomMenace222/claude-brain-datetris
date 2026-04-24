---
name: Branch naming — one number, one pair, never split
description: Every feature cycle = one number N with matching fix/<N>-backend, fix/<N>-frontend, fix/<N>-admin-panel branches. Never split a cycle number across multiple backend branches; never reuse N for a different feature.
type: feedback
originSessionId: c732c6f5-6b30-49a9-969c-571f3093382c
---
Every feature cycle gets ONE number `<N>`. Branches in that cycle must use the canonical per-role names:

- `fix/<N>-backend` — BackA20 work (or `fix/<N>-<descriptor>` only if there's a single branch in that cycle and no other role is involved)
- `fix/<N>-frontend` — FE app work
- `fix/<N>-admin-panel` — admin panel work (only if the cycle touches admin)

### Hard rules

- **Never split one cycle across multiple backend branches.** Cycle 112 was split into `fix/112-env-config` + `fix/112-api-referrals-me` — that was wrong. The right call was either: one `fix/112-backend` with both changes, OR make them cycles 112 and 113 (one feature each).
- **Never reuse a cycle number for a different feature,** even if the counterpart branch was skipped. If cycle 114 was backend-only, cycle 115 can have BE+FE; 114 stays frozen.
- **Never branch off a random tip.** Always cut from `development` tip at cycle start (or from an explicitly-named base for cherry-picks — and document it in the commit).
- **Naming must be consistent.** `fix/113-rank-one-grace` was wrong — should have been `fix/113-backend`. Descriptors are OK only when no counterpart exists; as soon as a second role joins the cycle, both branches get role-based names.
- **When in doubt, ASK the user before creating a branch.** A wrong branch name is cheap to avoid up-front and expensive to unwind after commits land.

### Past violations (do not repeat)

- **Cycle 112**: split into `fix/112-env-config` + `fix/112-api-referrals-me`. Both merged, no harm, but set a bad precedent.
- **Cycle 113**: used `fix/113-rank-one-grace` descriptor instead of `fix/113-backend`.

**Why:** User established this convention M107 (2026-04-24) after noticing the cycle-112 split and cycle-113 naming drift. The goal is unambiguous cross-role references: when the user says "cycle 115", there is exactly one BE branch and at most one FE branch and at most one admin-panel branch, each with a predictable name.

**How to apply:** Before `git checkout -b` on any fix branch, confirm the cycle number the user has in mind and use the role-based name. If I think a change belongs in the current cycle but the user hasn't said so, ask (e.g. "wire this into fix/115-backend, or cut fix/116 for it?"). If I think the scope warrants a new cycle, ask before minting one. For backend-only or frontend-only cycles, the counterpart branch is skipped — but the number is frozen to that cycle forever.
