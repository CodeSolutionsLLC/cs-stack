---
name: ship-change
description: >-
  Land an intentional feature or behavior change behind a human approval gate —
  state outcome and non-goals, implement in small commits, verify, open a PR,
  wait for CODEOWNERS review. Use for features (not pure bugfixes). Links
  playbooks/ship-change.md.
version: 0.1.0
---

# Ship change

Operationalizes [playbooks/ship-change.md](../../playbooks/ship-change.md).
Pair with [playbooks/verify.md](../../playbooks/verify.md) and
[playbooks/review-pr.md](../../playbooks/review-pr.md).

## When to use

- Intentional features or behavior changes (not pure bugfixes)
- Operator asks to ship, implement a feature, or land a change on `main`

## When not to use

- Pure defect with reproduction → use [fix-bug](../fix-bug/SKILL.md)
- Facts still missing → use [investigate](../investigate/SKILL.md)

## Agent steps

1. State the **user-visible outcome** in one paragraph. Include non-goals.
2. Sketch the touch surface: files, APIs, data, roll-back path.
3. Implement in **small commits** that each leave the tree understandable.
4. Add or update checks for the new behavior and regressions.
5. Run verification ([playbooks/verify.md](../../playbooks/verify.md)); paste key results into the PR.
6. Open a PR against the protected default branch. Summarize risk and how to validate.
7. **Wait for CODEOWNERS / human review. Do not self-merge.**
8. After merge (by a human), confirm the post-merge signal (CI green, smoke, or named follow-up).

## Success proof

- PR describes intent, risk, and verification
- Required checks are green; code-owner review requested (not self-approved)
- Rollback or follow-up named if residual risk remains

## Principles

- [human-gate](../../principles/human-gate.md) — humans approve what lands on `main`
- [no-self-merge](../../principles/no-self-merge.md)
- [verify](../../principles/verify.md)
- [careful](../../principles/careful.md)
- [no-vendor-upstream](../../principles/no-vendor-upstream.md)

## Do not

- Bypass branch protection or force-push to `main`
- Merge your own PR without an independent human gate
- Ship secrets, private scanner configs, or vendor upstream trees
- Copy upstream playbook/skill files into this repo
