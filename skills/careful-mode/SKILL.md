---
name: careful-mode
description: >-
  Standing rigor mode for cs-stack — concise, unslopped, verified,
  human-gated work. Use as the default posture on any change; when the
  operator asks for careful or rigor mode; before editing, shipping, or
  claiming done; or when a pass was speculative or unverified. Routes
  through the v0 loop (setup-cs-stack, investigate, fix-bug, ship-change,
  verify, review-pr) and principles (careful, verify, human-gate,
  no-self-merge). Does not add a second playbook catalog.
---

# Careful mode

Standing rigor posture for this stack. Prefer the smallest change that is
correct ([principles/careful.md](../../principles/careful.md)). This skill
selects and tightens the v0 loop — it does not replace those playbooks or
invent another catalog.

## When to use

- Default posture for work in this repository
- Operator asks for careful mode, rigor mode, or a tighter bar
- Before editing production paths, shipping, or claiming a change is done
- After a pass that was padded, speculative, or unverified

## When not to use

- First-run install only → [setup-cs-stack](../setup-cs-stack/SKILL.md)
- This skill is a posture, not a substitute for the playbook that matches
  the job (investigate / fix-bug / ship-change / verify / review-pr)

## v0 loop (do not expand)

Read enough context, then take **one** path. Do not invent extra procedures.

| Situation | Skill | Playbook |
|-----------|-------|----------|
| Fresh clone / wire skills | [setup-cs-stack](../setup-cs-stack/SKILL.md) | first-run configure |
| Wrong, unclear, or surprising | [investigate](../investigate/SKILL.md) | [playbooks/investigate.md](../../playbooks/investigate.md) |
| Confirmed, reproducible defect | [fix-bug](../fix-bug/SKILL.md) | [playbooks/fix-bug.md](../../playbooks/fix-bug.md) |
| Intentional feature or behavior | [ship-change](../ship-change/SKILL.md) | [playbooks/ship-change.md](../../playbooks/ship-change.md) |
| Need recorded evidence | [verify](../verify/SKILL.md) | [playbooks/verify.md](../../playbooks/verify.md) |
| Review or merge-readiness | [review-pr](../review-pr/SKILL.md) | [playbooks/review-pr.md](../../playbooks/review-pr.md) |

Scope of that set: [docs/PLAYBOOK-V0.md](../../docs/PLAYBOOK-V0.md).
Index: [skills/README.md](../README.md), [playbooks/README.md](../../playbooks/README.md).

## Standing rules

1. **Read first.** Enough context to name the owning boundary. If the next
   edit would still be a guess, investigate — do not patch.
2. **Stay concise.** No filler, no restating the playbook, no unearned
   certainty. Write the smallest correct change.
3. **Prove it.** No change is done until there is evidence
   ([principles/verify.md](../../principles/verify.md)). Record commands
   and key results on the PR. CI is necessary, not always sufficient.
4. **Stop at the human gate.** Agents open PRs and request review.
   Humans decide what lands on `main`
   ([principles/human-gate.md](../../principles/human-gate.md)).
5. **Do not self-merge.** Wait for an independent human approval
   ([principles/no-self-merge.md](../../principles/no-self-merge.md)).

## Success proof

- The matching v0 skill/playbook was followed, not paraphrased into a
  new procedure
- The diff is the smallest change that is correct
- Verification evidence is on the PR
- A human — not the author as sole voice — is the merge gate

## Principles

- [careful](../../principles/careful.md) — smallest correct change; investigate when unsure
- [verify](../../principles/verify.md) — evidence over speculation
- [human-gate](../../principles/human-gate.md) — agents propose; humans decide
- [no-self-merge](../../principles/no-self-merge.md) — independent approval before merge

## Do not

- Invent a second playbook catalog or expand v0 scope
- Patch while still guessing
- Pad the diff, the docs, or the PR with filler
- Claim done without recorded evidence
- Self-merge or bypass branch protection, CODEOWNERS, or required checks
- Vendor third-party playbook or skill trees
  ([no-vendor-upstream](../../principles/no-vendor-upstream.md))
