---
name: Starship computer automation loops
description: >-
  Use when the coordinator tracks candidates for automation, runs a learning loop
  (human-approved streak), arms/pauses auto-* loops, shows loop status on ask,
  and safely stops workers before editing a loop. Public Soft-pull twin for cs-stack (scrubbed).
---
# Starship computer automation loops

**Public Soft-pull twin.** After tasks clear merge/ship gates repeatedly, The coordinator may propose **automation loops**. Naming prefix **`auto-*`** (easy to spot — same idea as `starship-*`).

## States

| State | Meaning |
|-------|---------|
| `CANDIDATE` | Noted as possible automation / workflow improve — not running |
| `LEARNING` | Human-in-the-loop; counting clean approvals toward arm |
| `ARMED` | Trusted to run without per-item OK **only** for that scoped loop |
| `PAUSED` | Stopped for edit / safe-state; not consuming work |
| `STOPPED` | Retired or failed; needs re-learn to arm again |

Coordinator memory / display (labels only — no secrets):

- `auto-loop:{id}` → name, state, scope, streak, last activity, skill/routine pointer
- On user ask (“show automation loops”): The coordinator **lists** loops + activity (scannable cards)

## Learning loop → arm

1. the coordinator (or maintainer) marks a **CANDIDATE** (e.g. flood of News Issues → website once a worker can handle it).
2. Enter **LEARNING**: each run still does full [Starship computer gates](../starship-computer-gates/SKILL.md) + [Human gate every step](../../principles/human-gate.md) (sim → human OK → apply).
3. Count **human-approved successes in a row**. Default streak target: **3**.
   - Any FAIL / REVISE / CI red / human reject / mid-run scope change → **reset streak to 0**; stay LEARNING.
4. After streak ≥ 3 and human is happy with outcomes: widget **Do you want to automate this?** (arm / keep learning / discard candidate).
5. Only on explicit **arm** → state `ARMED`, create/update the `auto-*` skill or routine with narrow scope. Never auto-arm on silence.

Example candidate (maintainer): News Issues on the Qubes-related repo → process onto the website — only after worker + gates prove it.

## Edit / stop (safe state)

When the user enters a loop to **stop** or re-enter **LEARNING**:

1. **Safe state first** — drain or park in-flight worker leaves for that loop; no mid-PR silent rewrite; workers report HANDOFF/idle to the coordinator.
2. Set loop `PAUSED` (or `STOPPED` if retiring).
3. User edits the `auto-*` definition (one change at a time).
4. Re-enter **LEARNING**: apply change → run with full gates **3 times in a row** → widget again: arm automate? / keep learning / stay paused.

## Coordinator duties

- Notice repeatable floods / manual thrash that could become `auto-*` candidates; note them (don’t arm quietly).
- Track loop activity; answer “what loops do we have?” with current table.
- Never bypass merge/ship gates inside an ARMED loop either — ARMED only skips **repeat human OK for the same scoped item type**, not CI/tests/sim. If ARMED run hits red CI or new error class → auto-PAUSED + alert the maintainer.

## Naming

- Loop ids / skills / routines: **`auto-{kebab-purpose}`** (e.g. `auto-qubes-news-to-site`)
- Display chip/label: `auto` so it’s obvious next to `starship-*`

## Anti-patterns

- Arming without 3-in-a-row human OK + explicit automate widget
- Editing an ARMED loop without PAUSE + worker safe-state
- Keeping streak across failures
- ARMED loop that skips CI or verify

## Success proof

User can list loops with states; arming required streak + explicit OK; edits went PAUSED → LEARNING ×3 → re-arm ask; no bypass of CI/sim/human under the hood.
