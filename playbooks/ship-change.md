# Ship change

Use this playbook for intentional features or behavior changes (not pure
bugfixes). Pair with [verify.md](verify.md) and [review-pr.md](review-pr.md).

## Goal

Land a clear, reviewable change behind a human approval gate.

## Steps

1. **State the user-visible outcome** in one paragraph. Include non-goals.
2. **Sketch the touch surface**: files, APIs, data, and roll-back path.
3. **Implement in small commits** that each leave the tree understandable.
4. **Add or update checks** that prove the new behavior and guard regressions.
5. **Run verification** ([verify.md](verify.md)) and paste key results into
   the PR.
6. **Open a PR against the protected default branch**. Summarize risk and
   how to validate.
7. **Wait for CODEOWNERS / human review**. Do not self-merge.
8. **After merge**, confirm the expected post-merge signal (CI green,
   smoke check, or documented follow-up).

## Done when

- The PR describes intent, risk, and verification.
- Required checks and code-owner review have passed.
- Rollback or follow-up is named if residual risk remains.

## Do not

- Bypass branch protection or force-push to `main`.
- Merge your own PR without an independent human gate.
- Ship secrets, private scanner configs, or vendor upstream trees.
