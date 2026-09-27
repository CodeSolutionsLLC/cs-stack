---
name: Starship issue tracker
description: >-
  Use when maintaining a single per-repo tracking issue (tracker label) as the
  index of open issues, aligned with org deployment priority when a central
  board exists. Public Soft-pull twin for cs-stack (scrubbed).
---
# Starship issue tracker

**Public Soft-pull twin** (scrubbed for CodeSolutionsLLC/cs-stack). Generic git/GitHub/CI patterns for any Grok Bot user.

Maintain **one** open tracking issue per repository as the central index of open issues, prioritized by criticality. Prevents scattered tracking and gives a quick repo-health overview.

## When to use

- Starting work in a repo (check tracker exists)
- Review / organize / triage open issues
- Periodic maintenance
- Multiple trackers detected
- After [Starship issue workflow](../starship-issue-workflow/SKILL.md) closes/creates issues (lightweight tracker refresh)

## Human gates

Creating a tracker, consolidating (closing duplicates), or editing a live tracker body that changes what humans see → show the proposed body/diff and get explicit OK ([Human gate every step](../../principles/human-gate.md)). Silence ≠ OK.

## Process

### 1. Detect existing tracker

```bash
gh issue list --label "tracker" --state open --json number,title --jq '.[].number'
```

If no `tracker` label hits, search by title:

```bash
gh issue list --state open --search "Issue Tracker in:title" --json number,title --jq '.[].number'
```

Detect current repo with `gh repo view --json nameWithOwner` — do not hardcode org/repo names.

### 2. Handle results

| Result | Action |
| ------ | ------ |
| None | Create (step 3) after human OK |
| One | Update (step 4) after human OK if material |
| Multiple | Consolidate (step 5) after human OK |

### 3. Create tracker

1. Ensure label: create `tracker` if missing (`gh label create` with a short description).
2. Fetch open issues (`gh issue list --state open --json number,title,labels,createdAt --limit 200`).
3. Categorize (Priority mapping below).
4. Show proposed body → human OK → `gh issue create --title "Issue Tracker" --label tracker` (pin if supported; else note manual pin).

Body shape (titles + labels only — **never paste issue bodies**):

```markdown
# Issue Tracker

Central index of all open issues in this repository.

## Critical
- [ ] #N - title — labels

## High Priority
…

## Standard
…

## Low Priority
…

## Blocked
- [ ] #N - title — blocked by #M

---
*Last updated: YYYY-MM-DD*
*Auto-maintained tracking index. Do not close.*
```

Omit empty sections. Max 200 lines; if more: `*Showing 200 of N open issues*`.

### 4. Update existing tracker

1. Fetch tracker body + all open issues (exclude tracker itself).
2. Re-categorize; propose new body.
3. Human OK → `gh issue edit <n> --body …`
4. Closed-since-last-update: check off once, drop on next cycle.

### 5. Consolidate multiple trackers

1. Canonical = oldest (lowest number).
2. Merge unique index lines into canonical.
3. Human OK → comment + close duplicates pointing at canonical.
4. Update canonical (step 4).

## Priority mapping

Highest match wins (case-insensitive labels):

| Priority | Labels |
| -------- | ------ |
| Critical | `critical`, `security`, `vulnerability` |
| High | `bug`, `compliance`, `reliability`, `disaster-recovery` |
| Standard | `enhancement`, `feature`, `documentation`, `testing`, `workflow` (default if no match) |
| Low | `good first issue`, `help wanted`, `question`, `cleanup`, `code-quality` |
| Blocked | title/body mentions blocked |

Within a section: sort by issue number ascending (unless central-board rank overrides — below).

## Central board alignment (when present)

Two-level model when the org uses a **central deployment priority board** (e.g. an private ops-tracker issue labeled `deployment-tracker`):

- **Ordering:** central → repo. If this repo’s issues appear on the central board, order them inside their priority section to match board rank. Off-board issues: by issue number.
- **Content:** repo → central. Deployment-significant new items get **proposed** onto the central board at session closeout — this skill does **not** edit the central board during routine tracker maintenance.
- **Categories:** while indexing, if the org uses a single `cat:*` category label taxonomy, backfill missing `cat:*` on issues (one label) and show it on the tracker line.
- **Do not duplicate the board.** Never copy other repos’ board rows into a per-repo tracker.

If the coordinator’s private-ops-tracker attach is still **not-yet-bound**, do not invent a central board bind — keep per-repo tracker only.

## Security / scrub rules

- Titles + labels only in the tracker — never issue bodies (may hold sensitive detail)
- No secrets, tokens, PATs, cookies, vault IDs, customer PII in tracker text
- No CI-bypass guidance
- Use `gh` only; no hardcoded credentials

## Guidelines

- Index, not a project plan
- One open tracker; prefer pin
- Label `tracker` consistent across repos
- Prefer oldest tracker when consolidating (link history)
- If fewer than 3 open issues, creating a tracker is optional — ask

## Related

- [Starship issue workflow](../starship-issue-workflow/SKILL.md) (import wave)
- [Starship session closeout](../starship-session-closeout/SKILL.md) (import wave)
- [Starship github issue](../starship-github-issue/SKILL.md) (import wave)

## Success proof

Repo has ≤1 open tracker; body is titles/labels only; human OK recorded for create/consolidate/material edits.
