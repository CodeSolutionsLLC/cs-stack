---
name: verify
description: >-
  Run an explicit prove-it pass after a substantive change and before asking
  for merge — restate expected behavior, run targeted checks, smoke or
  lint/structure if the repo provides it, exercise happy and edge paths,
  confirm no secrets, paste evidence on the PR. Use when a change needs
  recorded evidence, not a bare LGTM. Links playbooks/verify.md.
version: 0.1.0
---

# Verify

Operationalizes [playbooks/verify.md](../../playbooks/verify.md).
No change is done until there is evidence
([principles/verify.md](../../principles/verify.md)).

## When to use

- After any substantive change, before asking for merge
- When the operator asks to verify, prove it, or attach evidence
- When CI is green but the failure mode sits outside what CI covers

## When not to use

- Facts still missing → follow [playbooks/investigate.md](../../playbooks/investigate.md) first
- Reviewing someone else’s PR → use [review-pr](../review-pr/SKILL.md)

## Agent steps

1. Restate the expected behavior in one sentence.
2. Run the targeted automated checks for the touched area.
3. Run a broader smoke or lint/structure check if the repo provides one
   (for example the `validate` workflow).
4. Exercise the happy path manually or via script when automation is thin.
5. Exercise at least one failure / edge path when risk warrants it.
6. Confirm no secrets or private tokens appear in the diff.
7. Confirm docs and playbook links still resolve if markdown changed.
8. Paste concise evidence (commands + key output) into the PR.

## Success proof

- Every applicable [playbooks/verify.md](../../playbooks/verify.md) checkbox is complete
- A reviewer can re-run the same checks from the PR notes
- Evidence is recorded — not implied by “LGTM” or green CI alone

## Principles

- [verify](../../principles/verify.md) — CI is necessary, not always sufficient
- [careful](../../principles/careful.md)
- [human-gate](../../principles/human-gate.md) — agents attach evidence; humans decide
- [no-vendor-upstream](../../principles/no-vendor-upstream.md)

## Do not

- Claim “LGTM” without recorded evidence
- Skip verification because “it is only docs” when links or structure matter
- Rely solely on CI if the failure mode is outside what CI covers
