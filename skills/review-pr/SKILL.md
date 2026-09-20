---
name: review-pr
description: >-
  Review someone else’s pull request with independent human judgment backed
  by evidence — read intent and verification notes, scan the diff, confirm
  CODEOWNERS and required CI, spot-check claims, approve or request changes.
  Authors do not self-merge. Use when reviewing a PR or asking for review.
  Links playbooks/review-pr.md.
version: 0.1.0
---

# Review PR

Operationalizes [playbooks/review-pr.md](../../playbooks/review-pr.md).
Protect `main` with an independent human judgment. Authors do not approve
or merge their own pull requests as the sole voice
([principles/no-self-merge.md](../../principles/no-self-merge.md)).

## When to use

- Reviewing someone else’s change
- Asking for review on a PR you authored (you still do not merge it)
- Checking whether a PR is ready for a human merge

## When not to use

- You authored the change and are the only reviewer — request an independent
  human; do not self-approve or self-merge
- The change still lacks a prove-it pass → use [verify](../verify/SKILL.md) first

## Agent steps

1. **Read the PR description** for intent, risk, and verification notes.
2. **Scan the diff** for scope creep, secrets, and accidental vendor drops.
3. **Check CODEOWNERS paths** and that the right owners are requested.
4. **Confirm CI**: `validate`, CodeQL, and any other required checks.
5. **Spot-check verification claims** — re-run a critical command if doubt remains.
6. **Leave concrete feedback** (file + concern + suggested direction).
7. **Approve only when** intent is clear, risk is acceptable, and evidence
   matches the change. Otherwise request changes.
8. **Stop at approval.** A human merges. Do not merge as the author, and do
   not merge as the sole reviewer of your own authorship.

## Success proof

- Approval or requested changes is recorded on the PR
- Blocking issues are fixed or explicitly deferred with owner + issue
- Merge, if any, is performed by an independent human — not the author
  acting as sole approver

## Principles

- [no-self-merge](../../principles/no-self-merge.md) — wait for independent human approval
- [human-gate](../../principles/human-gate.md) — agents propose; humans decide what lands on `main`
- [verify](../../principles/verify.md) — evidence must match the change
- [no-vendor-upstream](../../principles/no-vendor-upstream.md)
- [careful](../../principles/careful.md)

## Do not

- Rubber-stamp large diffs
- Self-merge as the sole reviewer on your own authorship
- Approve when required status checks are red or missing without cause
- Bypass branch protection, CODEOWNERS, or required checks
- Treat agent approval as a substitute for a human merge
