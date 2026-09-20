---
name: investigate
description: >-
  Gather facts before changing production behavior — restate the question,
  collect primary evidence, form a falsifiable hypothesis, run the smallest
  check, and record findings. Use when something is wrong, unclear, or
  surprising and a patch would still be a guess. Links playbooks/investigate.md.
version: 0.1.0
---

# Investigate

Operationalizes [playbooks/investigate.md](../../playbooks/investigate.md).
Do not duplicate that playbook here — follow it; this skill is the agent
entry point and success contract.

## When to use

- Behavior is wrong, unclear, or surprising
- Before editing production paths
- When the operator asks to investigate, dig in, or explain a failure

## When not to use

- The defect is already reproduced and localized → use [fix-bug](../fix-bug/SKILL.md)
- Intentional feature work → use [ship-change](../ship-change/SKILL.md)

## Agent steps

1. Restate the question (1–2 sentences) and what “answered” looks like.
2. Collect **primary evidence** (logs, failing tests, traces, API bodies, diffs). Prefer artifacts over recollection.
3. Name the owning boundary (service, package, or file).
4. Write a **falsifiable hypothesis** plus what would disprove it.
5. Run the **smallest check** that can confirm or reject it.
6. Record findings in the issue or PR: what you saw, what you ruled out, unknowns + next probe.

## Success proof

Hand back at least one of:

- Concrete evidence supporting the leading theory (path, command, output excerpt), or
- A short list of remaining unknowns, each with a next probe

## Principles

- [careful](../../principles/careful.md) — do not patch while guessing
- [verify](../../principles/verify.md) — evidence over speculation
- [no-vendor-upstream](../../principles/no-vendor-upstream.md) — first-party prose only

## Do not

- Patch while still guessing
- Expand into unrelated cleanups
- Treat chat speculation as evidence
- Copy upstream playbook files into this tree
