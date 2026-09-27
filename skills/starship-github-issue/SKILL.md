---
name: Starship github issue
description: >-
  Use when creating or drafting GitHub issues with team conventions — labels,
  body shape, no secrets; human gate before file. Public Soft-pull twin for cs-stack (scrubbed).
---
# Starship github issue

**Public Soft-pull twin** (scrubbed for CodeSolutionsLLC/cs-stack). Generic git/GitHub/CI patterns for any Grok Bot user.

Create well-formatted GitHub issues via `gh`: consistent titles/labels/body shape, route to the right repo, close the tracking loop at creation time when a per-repo tracker / central board is available. Complements [Starship issue workflow](../starship-issue-workflow/SKILL.md) (how issues are **worked**) and [Starship issue tracker](../starship-issue-tracker/SKILL.md) (per-repo index).

## When to use

- Bug / feature / security / docs / compliance / refactor needs a tracked issue
- Drafting an issue body for human review before filing
- Cross-repo or project-wide work that needs a correct target repo

## Hard rules

- [Human gate every step](../../principles/human-gate.md) — **show the full proposed title, labels, body, and target repo; get explicit OK before `gh issue create`**. Silence ≠ OK. Draft-only is fine until gated.
- **No secrets** — never paste tokens, PATs, cookies, vault IDs, private keys, passwords, or customer PII into titles/bodies/comments. Sanitize error output and usernames.
- Soft-fail if the central deployment board is unbound / coordinator private-ops-tracker attach is **not-yet-bound** — keep per-repo create + tracker only; **do not invent a board bind**.
- Soft-fail if no `tracker`-labelled issue exists — still file the issue after human OK; note that tracker sync was skipped and point at [Starship issue tracker](../starship-issue-tracker/SKILL.md).
- Use `gh` only; no hardcoded credentials. Detect repos with `gh repo view` — do not hardcode org/repo names as defaults.

## Prerequisites

```bash
gh --version
gh auth status
```

## Process

### 1. Determine target repository

```bash
CURRENT_REPO=$(gh repo view --json nameWithOwner --jq '.nameWithOwner')
echo "Current repo: $CURRENT_REPO"
```

| Issue type | Target |
| ---------- | ------ |
| Code/config in the current repo | Current repo (default) |
| Cross-repo / project-wide / unclear | Ask the operator |
| Known other fleet repo (legal, config, main project, …) | That repo — only when operator confirms |

**Default:** file in `$CURRENT_REPO` unless the issue clearly belongs elsewhere. Set `TARGET_REPO` accordingly; never silently file into a hardcoded org name.

### 2. Search for duplicates

```bash
gh issue list --repo "$TARGET_REPO" --search "keyword" --state open
```

If a duplicate or near-duplicate exists, propose linking/commenting instead of creating — human decides.

### 3. Verify labels

```bash
gh label list --repo "$TARGET_REPO" --json name --jq '.[].name' | sort
```

Only apply labels that exist. Missing label: create with `gh label create` **after human OK**, or omit and note in the body. Prefer exactly one `cat:*` category when the repo uses that taxonomy (see step 6a).

### 4. Draft the issue (do not file yet)

Use a title prefix: `Bug:`, `Feature:`, `Security:`, `Docs:`, `NIST:`, `Refactor:` (or repo convention). Fill the matching body shape below. Show the operator:

- Target repo
- Title
- Labels (incl. planned `cat:*`)
- Full body

Wait for explicit OK before step 5.

#### Bug

```markdown
## Bug Report

### Summary
<!-- One-line description -->

### Environment
<!-- Repo-relevant environment only — no secrets -->

### Steps to Reproduce
1.
2.

### Expected Behavior

### Actual Behavior

### Error Output
    (sanitize secrets / usernames)

### Related
- NIST Control: <!-- if applicable -->
- Related Issues: <!-- #N -->
```

Labels: `bug` (+ `cat:*` when used).

#### Feature

```markdown
## Feature Request

### Summary

### Problem Statement

### Proposed Solution

### NIST SP 800-53 Alignment
- Control: <!-- if applicable -->
- Justification:

### Acceptance Criteria
- [ ]
- [ ]
```

Labels: `enhancement` (+ `cat:*`).

#### Security

```markdown
## Security Issue

### Classification
- **Severity**: <!-- Critical / High / Medium / Low -->
- **Type**: <!-- Vulnerability / Hardening / Audit Finding -->

### Summary

### Affected Components
<!-- Files, services, systems — no credentials -->

### NIST SP 800-53 Control
- **Control ID**:
- **Control Name**:

### Recommended Remediation

### Verification Steps
```

Labels: `security` (+ `cat:*`). Never include exploit PoCs or live secrets.

#### Documentation

```markdown
## Documentation Update

### Type
- [ ] New documentation
- [ ] Update existing docs
- [ ] Fix inaccuracies

### Affected Document(s)

### Summary

### Proposed Changes
```

Labels: `documentation` (+ `cat:*`).

#### NIST compliance

```markdown
## NIST SP 800-53 Compliance

### Control Information
- **Control ID**:
- **Control Name**:
- **Control Family**:

### Current Status
- [ ] Not Implemented
- [ ] Partially Implemented
- [ ] Implemented - Needs Verification

### Gap Analysis

### Implementation Plan

### Acceptance Criteria
- [ ] Control requirement met
- [ ] Evidence documented
- [ ] Verified in target environment
```

Labels: `compliance` (+ `cat:*`).

#### Refactor

```markdown
## Refactoring

### Motivation

### Scope

### Proposed Changes

### Testing Requirements
- [ ] Existing tests pass
- [ ] New tests added for changed behavior
```

Labels: `enhancement` (+ `cat:*`).

### 5. Create (human OK required)

```bash
gh issue create --repo "$TARGET_REPO" \
  --title "…" \
  --label "…" \
  --body-file /tmp/issue-body.md
```

Prefer `--body-file` over inline heredocs when the body is long. Note the issue number/URL. Inform the operator.

### 6. Close the loop at creation time

An issue that exists but is on no tracker is easy to miss. Sync at creation when possible — do **not** defer solely to session closeout (closeout may never run if a session dies). Soft-fail honestly when infra is missing.

#### 6a. Apply a `cat:*` category (when the repo uses it)

Exactly one `cat:*` from the repo/org taxonomy. Uncategorised issues cannot participate in disjoint-surface concurrency and are treated conservatively as conflicting.

#### 6b. Add to the repo tracker (when present)

```bash
TRACKER=$(gh issue list --repo "$TARGET_REPO" --label tracker --state open \
  --json number,createdAt --jq 'sort_by(.createdAt) | reverse | .[0].number')
```

- **None** → soft-fail; note skip; optionally offer [Starship issue tracker](../starship-issue-tracker/SKILL.md).
- **One** → add the new issue to the right priority section per that skill (titles/labels only; human OK if the tracker edit is material).
- **More than one** → **stop**; do not guess. Epics/umbrellas carry `umbrella`, not `tracker`. Fix labels first (see starship-issue-tracker consolidate).

#### 6c. Central board — deliberate, soft-fail if unbound

If the coordinator has an active private ops tracker / `deployment-tracker` board bind:

Update the board **only** when the new issue changes **cross-repo deployment order**. Do **not** mirror every issue.

| Add a row when | Why |
| -------------- | --- |
| Active breakage (CI red, service down, credential dead) | Blocks org-wide work |
| Hard deadline / external clock | Outranks unclocked work |
| Blocks or unblocks another repo | Board decides cross-repo order |
| Security incident | Escalation, not backlog |

Otherwise leave the board alone — the per-repo tracker is the index; the board is the order. When adding a row: place by consequence, state reasoning in Notes, bump `Last synced`, re-fetch immediately before write (read-modify-write). **Human OK** before board edit.

If board missing or attach is **not-yet-bound**: soft-fail, skip board, do not invent a bind. If unsure whether it belongs on the board, **ask the operator** rather than silently skipping a possible P0.

### 7. Hand off

- Reference in commits: `Resolves #N`
- Work the issue via [Starship issue workflow](../starship-issue-workflow/SKILL.md)
- Align / dedupe later via [Starship issue alignment](../starship-issue-alignment/SKILL.md) when backlog needs it

## Listing helpers

```bash
gh issue list --repo "$TARGET_REPO"
gh issue list --repo "$TARGET_REPO" --label "bug"
gh issue view <number> --repo "$TARGET_REPO"
gh issue comment <number> --repo "$TARGET_REPO" --body "…"
gh issue close <number> --repo "$TARGET_REPO"
```

Closing/bulk absorb → prefer alignment skill + human gate; do not mass-close from this skill.

## Anti-patterns

- Filing without human OK on title/labels/body/repo
- Pasting secrets, tokens, or raw credential errors into issues
- Hardcoding org/repo names as silent defaults
- Hard-requiring foreign slash commands or private box-only hooks
- Inventing a central-board bind when unbound / not-yet-bound
- Adding every new issue to the deployment board
- Updating the wrong tracker when multiple `tracker` labels exist

## Related

- [Starship issue tracker](../starship-issue-tracker/SKILL.md)
- [Starship issue workflow](../starship-issue-workflow/SKILL.md)
- [Starship issue alignment](../starship-issue-alignment/SKILL.md)
- [Human gate every step](../../principles/human-gate.md)
- [Starship session closeout](../starship-session-closeout/SKILL.md) (when imported — not a substitute for creation-time sync)
- [Starship create pr](../starship-create-pr/SKILL.md) (when imported)

## Success proof

Human OK recorded before create; issue in the correct repo with clear title/labels; no secrets in body; `cat:*` applied when taxonomy exists; tracker updated or soft-fail noted; central board considered only when bound (row added with reasoning, consciously skipped, or soft-failed unbound); operator given issue URL.
