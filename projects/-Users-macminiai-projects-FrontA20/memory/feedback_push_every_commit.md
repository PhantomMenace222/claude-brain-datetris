---
name: Push fix branches after every commit
description: Every commit on a fix/<desc> branch must be pushed to origin immediately, not deferred to merge time, so work survives local machine failure
type: feedback
originSessionId: 2f51a5b5-fa5a-4230-b6bc-8c1970eca761
---
**The rule:** Every commit on a `fix/<desc>` branch is pushed to `origin` immediately.

```
# after any commit on a fix branch:
git push origin <fix-branch>
# or, if the branch has no upstream yet:
git push -u origin <fix-branch>
```

**Why:** User's Mac could die at any moment. Work that exists only in a local commit is lost until GitHub has it. Waiting until merge (possibly many commits later) means hours of unpushed work at risk. One extra `push` per commit is cheap.

**How to apply:**
- After every `git commit` on a `fix/<desc>` branch, run the push as the very next action.
- First push of a new branch uses `-u` so subsequent pushes don't need arguments.
- If the push fails (offline, network hiccup), flag it but don't block local work — try again with the next commit.
- Does NOT apply to `development`: that still goes through the normal merge + push-once flow. But since each fix branch is being pushed after every commit anyway, development pushes at merge time are just bringing origin's `development` ref up to date.
- Does NOT apply to squash / WIP / amend scratch work that the user explicitly marks as local-only.
