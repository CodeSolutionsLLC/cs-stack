---
name: Starship issue workflow
description: >-
  Use when working a GitHub issue end-to-end — discovery before code, progress
  updates, completion summary; coordinate ownership via coordinator/ops board when
  available; human confirm before implement and before merge. Public Soft-pull twin for cs-stack (scrubbed).
---
# Starship issue workflow

**Public Soft-pull twin.** Scrubbed from a private instruction tree (not for public) `issue-workflow`. Complements [Starship github issue](../starship-github-issue/SKILL.md) (create) by governing how issues are **worked**.

## When to use

- "Work on #N" / assigned implement
- Resume a previously started issue

## Hard rules

- [Human gate every step](../../principles/human-gate.md) — discovery approach OK before implement; merge only after merge/ship gates
- [Starship computer gates](../starship-computer-gates/SKILL.md) — no CI bypass
- Never invent ownership — if another session owns the issue, stop and coordinate
- Soft-fail if coordination infra is down — never block pure offline analysis; do block conflicting edits when ownership is known

## Phase 1 — Discovery (before implementation)

### 1. Read the issue

```bash
gh issue view <number> --comments
```

Review body, labels, assignees, comments.

### 2. Prior session / tracker cue (advisory)

If a per-repo [Starship issue tracker](../starship-issue-tracker/SKILL.md) exists, skim latest closeout/recommendation comments when present. If a recommendation is >7 days old or for another repo, warn and ask the operator before proceeding. **Never auto-redirect** — operator decides.

### 3. Central board alignment (when available)

If the coordinator has an active private ops tracker / `deployment-tracker` board bind:

1. Read the board (highest open, unclaimed, non-conflicting pickup).
2. If `#N` mismatches higher open board items, surface the mismatch and ask whether to proceed.

Advisory only. If board missing or attach is **not-yet-bound**, skip — do not invent a bind.

### 4. Take ownership (coordinate)

Prefer the coordinator's ownership claim when your team uses a private ops tracker:

- **Same issue claimed by another active session → hard stop** (coordinate, pick another, or explicit co-own OK)
- **Files already on an open PR you don't own → hard stop** (amend that branch, open an amendment path, or explicit force with justification)
- **Related work / soft overlap → warn**, check disjoint paths, proceed only with judgment

If coordination tools/network unavailable: log soft-fail and continue discovery only; re-check before editing shared files.

### 5. Explore codebase

Identify files, deps, tests, risks. Do not start implementation yet.

### 6. Post discovery comment

Post a `## Discovery Analysis` comment on the issue covering relevant files, findings, proposed approach, risks, and estimated scope (Small/Medium/Large). Adapt sections to the issue.

```bash
gh issue comment <number> --body-file <discovery.md>
```

### 7. Human confirm approach

Ask for OK on the proposed approach before Phase 2. Silence ≠ OK.

### 8. Clean tree check

Before edits: expected branch + `git status --porcelain` clean (or only files this session will touch). Unexpected mods → investigate (parallel session?) — don't blindly stash/restore.

## Phase 2 — Progress (during implementation)

### Branch

One leaf / one branch. Prefer coordinator/maintainer naming conventions for the repo. Avoid colliding with other sessions' branches. Worktree isolation is fine when the clone is contended — not required when alone.

### Implement

- Smallest change that satisfies acceptance ([careful-mode](../careful-mode/SKILL.md))
- Local CI first (Local CI env install + [Starship computer CI delta baseline](../starship-computer-ci-delta-baseline/SKILL.md))
- Post short progress comments on material blockers only (quiet otherwise)

### Kickbacks

On CI fail / thin PR: fix cited items only; re-report. No scope creep.

## Phase 3 — Completion

1. Prove acceptance (tests, script, screenshot as appropriate)
2. Open/update PR per [Starship create pr](../starship-create-pr/SKILL.md) when imported
3. [Verify before merge](../verify/SKILL.md) + merge/ship gates → **human merge only**
4. Post completion summary on the issue (what changed, how to verify, leftover risks)
5. Lightweight [Starship issue tracker](../starship-issue-tracker/SKILL.md) refresh if a tracker exists
6. Release ownership / check in files via coordinator coordination when used
7. [Starship session closeout](../starship-session-closeout/SKILL.md) when imported for next-session notes

## Anti-patterns

- Coding before discovery comment + human approach OK
- Forcing co-own / PR-overlap without explicit operator OK
- Hard-requiring foreign-tool hooks or private box tool paths
- Editing the central deployment board from this skill during routine work
- Auto-merge or skipping CI

## Related

- [Starship issue tracker](../starship-issue-tracker/SKILL.md)
- [Starship github issue](../starship-github-issue/SKILL.md)
- [Starship computer gates](../starship-computer-gates/SKILL.md)
- [Starship create pr](../starship-create-pr/SKILL.md) (when imported)
- [Starship session closeout](../starship-session-closeout/SKILL.md) (when imported)
- [Human gate every step](../../principles/human-gate.md)

## Success proof

Discovery posted + approach OK'd; ownership clear or soft-failed honestly; PR CI-green + verified; human merge decision; tracker/closeout touched when applicable.
