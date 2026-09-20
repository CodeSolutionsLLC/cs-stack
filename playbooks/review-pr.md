# Review PR

Use this playbook when reviewing someone else’s change (or when asking for
review). Authors do not approve their own merges.

## Goal

Protect `main` with an independent human judgment backed by evidence.

## Steps

1. **Read the PR description** for intent, risk, and verification notes.
2. **Scan the diff** for scope creep, secrets, and accidental vendor drops.
3. **Check CODEOWNERS paths** and that the right owners are requested.
4. **Confirm CI**: `validate`, CodeQL, and any other required checks.
5. **Spot-check verification claims** — re-run a critical command if doubt
   remains.
6. **Leave concrete feedback** (file + concern + suggested direction).
7. **Approve only when** intent is clear, risk is acceptable, and evidence
   matches the change. Otherwise request changes.

## Done when

- Approval or requested changes is recorded on the PR.
- Blocking issues are either fixed or explicitly deferred with owner + issue.

## Do not

- Rubber-stamp large diffs.
- Self-merge as the sole reviewer on your own authorship.
- Approve when required status checks are red or missing without cause.
