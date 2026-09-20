# Fix bug

Use this playbook for a confirmed defect with a reproducible signal.

## Goal

Restore correct behavior with a minimal, verified change.

## Steps

1. **Reproduce** with a reliable command, test, or request. Capture the
   failing output.
2. **Bisect or localize** to the owning module. Prefer a failing test that
   names the regression.
3. **Write or tighten a check** that fails on the bug and will pass after the
   fix (automated when practical).
4. **Apply the smallest fix** that addresses the root cause. Avoid drive-by
   refactors in the same change.
5. **Verify** using [verify.md](verify.md): the new check passes, and nearby
   regression risks are covered.
6. **Document** the cause and the fix in the PR description (what broke,
   why, how we know it is fixed).

## Done when

- Reproduction no longer fails.
- Verification checklist for this change is complete.
- A human reviewer can follow the evidence without re-deriving it.

## Do not

- Ship “works on my machine” without a recorded verification step.
- Bundle unrelated features with the bugfix.
- Self-merge without the human gate.
