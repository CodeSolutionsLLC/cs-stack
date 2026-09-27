---
name: Starship computer gates
description: >-
  Use when the coordinator (or workers under it) would merge, ship, or “go green” —
  all-systems-green before light: necessity/security check, tests exist and
  passed, simulation shown, human gate; never bypass CI/tests/sim/human.
  Public Soft-pull twin for cs-stack (scrubbed).
---
# Starship computer gates

**Public Soft-pull twin.** Do **not** merge / ship / arm automation unless **all systems are green**.

## Pre-light checklist (every merge / ship / arm)

Before asking human merge or declaring a leaf done:

1. **Necessity** — does this system/change need to exist? Is there a simpler or **more secure** way to pass the same signal?
2. **Tests** — proper tests exist for the failure modes we know; they ran; they **passed**. Prefer [Starship computer CI delta baseline](../starship-computer-ci-delta-baseline/SKILL.md): valid baseline, then **delta + touched systems** only; Local CI env install before remote. No skip / `--no-verify` / force-merge. Untouched systems skipped for cost — **drills** must still prove gate loops catch misses.
3. **Simulation** — change was simulated / previewed / verified against the real artifact ([verify-before-merge](../verify/SKILL.md) when UI/user-facing).
4. **Human gate** — simulated/draft state shown; explicit OK ([Human gate every step](../../principles/human-gate.md)). Silence ≠ OK.

Tokens (and rockets) are expensive. Prefer one gated change that is proven over thrash.

## Never bypass

Forbidden without an explicit maintainer override for **that** act:

- Disabling or skipping required CI / status checks
- Force-push to protected defaults to “make it green”
- Merging on red or missing checks
- Skipping simulation / verify-before-merge when the leaf needs it
- Skipping human gate because “CI was green”
- Workers peer-shopping around the coordinator’s gates

CI green ≠ human OK. Human OK ≠ CI optional.

## Fits with

- [Starship computer automation loops](../starship-computer-automation-loops/SKILL.md)
- Engineering supervisor loop
- [Starship computer CI delta baseline](../starship-computer-ci-delta-baseline/SKILL.md)
- Local CI env install

## Anti-patterns

- “Ship it, we’ll fix CI later”
- Treating draft LGTM as launch
- Lighting automation while checks are yellow/unknown
- Full remote suite every leaf when a valid baseline + delta scope would do
- Shipping after CI config change without a new baseline

## Success proof

Leaf/ship/arm has: necessity note (or N/A), green required checks, sim/verify pointer, and recorded human OK — or an explicit stop with reason.
