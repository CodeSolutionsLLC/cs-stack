# Verify

Use this playbook after any substantive change, and before asking for merge.

## Goal

Produce evidence that the change does what it claims and did not break the
nearby contract.

## Checklist

- [ ] Restate the expected behavior in one sentence.
- [ ] Run the targeted automated checks for the touched area.
- [ ] Run a broader smoke or lint/structure check if the repo provides one
      (for example the `validate` workflow).
- [ ] Exercise the happy path manually or via script when automation is thin.
- [ ] Exercise at least one failure / edge path when risk warrants it.
- [ ] Confirm no secrets or private tokens appear in the diff.
- [ ] Confirm docs and playbook links still resolve if markdown changed.
- [ ] Paste concise evidence (commands + key output) into the PR.

## Done when

Every applicable checkbox is complete, and a reviewer can re-run the same
checks from the PR notes.

## Do not

- Claim “LGTM” without recorded evidence.
- Skip verification because “it is only docs” when links or structure matter.
- Rely solely on CI if the failure mode is outside what CI covers.
