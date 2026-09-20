---
name: fix-bug
description: >-
  Fix a confirmed, reproducible defect with a minimal verified change —
  reproduce, localize, add a failing check, apply the smallest fix, verify,
  document. Use when the bug signal is reliable. Links playbooks/fix-bug.md.
version: 0.1.0
---

# Fix bug

Operationalizes [playbooks/fix-bug.md](../../playbooks/fix-bug.md).
Pair verification with [playbooks/verify.md](../../playbooks/verify.md).

## When to use

- Confirmed defect with a reproducible command, test, or request
- Regression with a clear failing signal

## When not to use

- Cause still unknown → use [investigate](../investigate/SKILL.md) first
- Intentional feature / behavior change → use [ship-change](../ship-change/SKILL.md)

## Agent steps

1. **Reproduce** — capture failing output from a reliable command/test/request.
2. **Localize** — owning module; prefer a failing test that names the regression.
3. **Tighten a check** that fails on the bug and will pass after the fix.
4. **Apply the smallest fix** for the root cause. No drive-by refactors.
5. **Verify** per [playbooks/verify.md](../../playbooks/verify.md) — new check passes; nearby risks covered.
6. **Document** in the PR: what broke, why, how we know it is fixed.
7. Open a PR; request human review. **Do not self-merge.**

## Success proof

- Reproduction no longer fails (command + before/after or test name)
- Verification checklist complete (cite [playbooks/verify.md](../../playbooks/verify.md))
- PR evidence a human can follow without re-deriving it

## Principles

- [verify](../../principles/verify.md)
- [human-gate](../../principles/human-gate.md) — agents propose; humans decide
- [no-self-merge](../../principles/no-self-merge.md) — wait for independent approval
- [no-vendor-upstream](../../principles/no-vendor-upstream.md)

## Do not

- Ship “works on my machine” without a recorded verification step
- Bundle unrelated features with the bugfix
- Self-merge or bypass branch protection / CODEOWNERS
