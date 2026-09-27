---
name: Starship pr review
description: >-
  Use when reviewing a pull request for coding work — correctness, CI,
  security, human gate before approve/request-changes. Public Soft-pull twin for cs-stack (scrubbed).
---
# Starship pr review

**Public Soft-pull twin** (scrubbed for CodeSolutionsLLC/cs-stack). Generic git/GitHub/CI patterns for any Grok Bot user.

Guide structured, multi-perspective pull request reviews for coding work: correctness, CI awareness, security, and a **human gate before approve / request-changes**. Complements [Starship create pr](../starship-create-pr/SKILL.md) (opening the PR) and [Starship commit](../starship-commit/SKILL.md) (commits on the branch). Merge readiness still goes through [Starship computer gates](../starship-computer-gates/SKILL.md) + [Verify before merge](../verify/SKILL.md).

## When to use

- Reviewing a PR opened for a work leaf / issue
- Checking correctness, security, tests, and ops impact before a verdict
- Producing a four-bucket disposition report (act-on / consider / noted / dismissed)
- Before submitting approve, request-changes, or comment-only via `gh pr review`

## Hard rules

- [Human gate every step](../../principles/human-gate.md) — **before approve and before request-changes**. Show verdict draft, act-on list (if any), CI status summary, and the review body; get explicit OK. Silence ≠ OK. Comment-only drafts may be posted after secret/diff review when session policy permits; promoting to APPROVE or REQUEST CHANGES still needs human OK.
- **No secrets** — never include credentials, tokens, `.env` contents, private keys, vault IDs, cookies, or customer PII in review bodies, inline comments, or pasted diffs. Sanitize error output and CI logs before quoting.
- [Starship computer gates](../starship-computer-gates/SKILL.md) — no CI bypass; never treat approve as a merge license on red/missing checks; never advise `--no-verify` / force-merge / skip required status checks.
- Prefer Local CI env install + [Starship computer CI delta baseline](../starship-computer-ci-delta-baseline/SKILL.md) evidence when the PR claims local green; still confirm remote required checks.
- Soft-fail if coordination infra is down — still produce the review report locally and hold the `gh pr review` submit until human OK / infra recovers; do not invent ownership of another session's review thread or dismiss their findings without reason.

## Prerequisites

```bash
gh --version
gh auth status
gh pr view <number> --json number,title,state,isDraft,baseRefName,headRefName,statusCheckRollup,url
```

Know the PR number (or URL). Prefer reviewing from a clean checkout of the head SHA when you need to run local checks — do not mutate another session's worktree without coordination.

## 5-step review process

### 1. Gather context

Understand the PR before reviewing code.

```bash
# View PR details
gh pr view <number>

# See all changed files
gh pr diff <number> --name-only

# See full diff
gh pr diff <number>

# Check PR comments and review history
gh api repos/{owner}/{repo}/pulls/<number>/comments

# CI / status checks
gh pr checks <number> || gh pr view <number> --json statusCheckRollup
```

Read the PR description, linked issues, prior review comments, and whether required checks are green / pending / failing. Scan the diff for secrets before quoting anything:

```bash
gh pr diff <number> | grep -iE 'password|secret|token|api_key|private_key' || echo "Clean"
```

Real secrets in the diff → **act-on** (must scrub at a new SHA); do not paste secret values into the review body.

### 2. Understand scope

Categorize the changes:

- **Size**: How many files and lines changed?
- **Type**: Feature, bug fix, refactor, docs, config?
- **Risk**: Does it touch auth, data handling, infrastructure, or public APIs?
- **Dependencies**: Does it change dependencies or shared libraries?
- **CI**: Are required checks green? Any missing / skipped / failed gates?

Large or high-risk PRs warrant extra weight on security + testing lenses and a cautious verdict when unsure.

### 3. Multi-perspective review

Review the diff through each of these five lenses:

#### Perspective 1: Functionality

- Does the code do what the PR description says?
- Are edge cases handled?
- Is the logic correct and complete?
- Are there off-by-one errors, null/undefined cases, or race conditions?

#### Perspective 2: Code quality

- Is the code readable and well-organized?
- Are names descriptive and consistent with the codebase?
- Is there unnecessary duplication?
- Are abstractions at the right level — not over-engineered, not under-abstracted?
- Does it follow existing patterns in the codebase?

#### Perspective 3: Security

- Are user inputs validated and sanitized?
- Is authentication and authorization handled correctly?
- Are secrets kept out of code and logs?
- Are there injection risks (SQL, command, XSS)?
- Are file operations safe (no path traversal)?
- Are error messages safe (no internal details leaked)?

**Security checklist for sensitive PRs** (auth, data handling, infrastructure):

- [ ] Input validation at all entry points
- [ ] No hardcoded credentials or secrets
- [ ] Proper error handling without information leakage
- [ ] Access control checks in place
- [ ] Audit logging for sensitive operations
- [ ] Dependencies checked for known vulnerabilities

#### Perspective 4: Testing

- Are there tests for new functionality?
- Do tests cover edge cases and error paths?
- Are tests meaningful (not just testing implementation details)?
- Is test coverage adequate for the risk level?
- Do existing tests still pass? Prefer evidence from Local CI env install / delta baseline when claimed, plus remote `gh pr checks`.

#### Perspective 5: Operations & performance

- Are there performance concerns (N+1 queries, large allocations, blocking calls)?
- Is logging adequate for debugging in production?
- Are there monitoring or alerting implications?
- Will this change require migration or deployment coordination?
- Is backwards compatibility maintained where needed?
- Does merge require pre-light (necessity / tests / sim / human) per [Starship computer gates](../starship-computer-gates/SKILL.md)?

### 4. Produce review report

Structure findings in the closed four-bucket disposition vocabulary — the same buckets the ledger path uses. Every finding names the **raising reviewer** and the **rule** that produced it. `act-on` and `dismissed` each carry a one-line blast-radius. Publish the **Dismissed** set whenever any finding is dismissed — an invisible dismissal is rejected.

```markdown
## PR Review: #<number> — <title>

### Verdict: [APPROVE | REQUEST CHANGES | COMMENT]

### CI / gates snapshot

- Required checks: [green | pending | failing | missing]
- Notes: <one line — e.g. local delta baseline OK; remote still pending>

### Act on (must fix at a new SHA — re-review mandatory)

- [ ] **[File:line]** Description — raised by <reviewer>, rule: <rule> — blast-radius: <one line>

### Consider (follow-up issue; record the issue number)

- [ ] **[File:line]** Description — raised by <reviewer>, rule: <rule> — issue #<N>

### Noted (recorded, no action; reason mandatory)

- **[File:line]** Description — raised by <reviewer>, rule: <rule> — reason: <why no action>

### Dismissed (reason mandatory — PUBLISH this set)

- **[File:line]** Description — raised by <reviewer>, rule: <rule> — reason: <why dismissed> — blast-radius: <one line>

### Positive Notes

- Highlight what was done well
```

**Four-bucket guide** (maps onto ledger `fixed`/`waived`; `dropped` stays prohibited):

| Bucket    | Criteria / mapping                                             | Blocks Merge? |
| --------- | -------------------------------------------------------------- | ------------- |
| act-on    | Bugs, security, data-loss risk — fixed at a new SHA, re-review | Yes           |
| consider  | Important but deferrable — file follow-up issue, record #      | Usually       |
| noted     | Recorded, no action — reason mandatory                         | No            |
| dismissed | Not actionable / out of scope — reason + published set         | No            |

### 5. Submit review (human gate first)

**Stop for human OK** before `--approve` or `--request-changes`. Show the draft report (verdict + buckets + CI snapshot). After explicit OK:

```bash
# Approve (only after human OK; CI green or explicitly waived by operator)
gh pr review <number> --approve --body-file /tmp/pr-review-body.md

# Request changes (only after human OK)
gh pr review <number> --request-changes --body-file /tmp/pr-review-body.md

# Comment only (no verdict) — still sanitize; human OK when policy requires
gh pr review <number> --comment --body-file /tmp/pr-review-body.md
```

Write the four-bucket report to `/tmp/pr-review-body.md` (or a session-local path) before submitting — prefer `--body-file` over inline heredocs so the gated draft matches what is posted.

For inline comments on specific lines:

```bash
gh api repos/{owner}/{repo}/pulls/<number>/comments \
  -f body="Comment text" \
  -f commit_id="$(gh pr view <number> --json headRefOid -q .headRefOid)" \
  -f path="path/to/file" \
  -F line=42 \
  -f side="RIGHT"
```

Never paste secrets into inline comments. Prefer file:line references over dumping sensitive hunks.

**Approve ≠ merge.** After APPROVE, merge still requires [Starship computer gates](../starship-computer-gates/SKILL.md) (necessity, green checks, sim/verify when needed, human OK). Do not merge from this skill alone.

## Verdict decision guide

Same four buckets as Step 4 — one vocabulary for human PR review and machine-checked disposition.

| Situation                                          | Buckets present       | Verdict                                 |
| -------------------------------------------------- | --------------------- | --------------------------------------- |
| No findings, or only noted / dismissed             | noted, dismissed      | APPROVE (after human OK + CI snapshot)  |
| consider only (follow-ups filed, numbers recorded) | consider              | APPROVE with comments                   |
| Any act-on                                         | act-on                | REQUEST CHANGES                         |
| Need more context or discussion                    | (none yet)            | COMMENT                                 |
| Security concerns in sensitive areas               | act-on (err cautious) | REQUEST CHANGES                         |
| Required CI red / missing and merge is intended    | act-on or hold        | REQUEST CHANGES or COMMENT — do not APPROVE as merge-ready |

## NIST alignment (advisory)

- **SA-11 (Developer Testing)**: Review verifies test adequacy
- **CM-3 (Change Control)**: PR review is the change approval gate
- **RA-5 (Vulnerability Scanning)**: Security perspective catches vulnerabilities

## Anti-patterns

- Approving or requesting changes without human OK
- Approving as merge-ready while required CI is red/missing
- Secrets in review bodies, inline comments, or pasted logs
- Invisible dismissals (dismissed set not published)
- Dropping findings without bucket + reason (`dropped` prohibited)
- Skipping security lens on auth / data / infra PRs
- Hard-requiring foreign session IDs, slash commands, or private box-only hooks
- Inventing ownership of another session's review thread
- Treating this skill's APPROVE as merge license

## Related

- [Starship create pr](../starship-create-pr/SKILL.md)
- [Starship commit](../starship-commit/SKILL.md)
- [Starship issue workflow](../starship-issue-workflow/SKILL.md)
- [Starship computer gates](../starship-computer-gates/SKILL.md)
- Local CI env install
- [Starship computer CI delta baseline](../starship-computer-ci-delta-baseline/SKILL.md)
- [Verify before merge](../verify/SKILL.md)
- [Human gate every step](../../principles/human-gate.md)
- [Starship github issue](../starship-github-issue/SKILL.md)

## Success proof

Context gathered (diff + CI snapshot + secret scan); five lenses applied; four-bucket report complete (dismissed published when used); human OK recorded before approve/request-changes; no secrets in review text; merge-ship gates/CI not bypassed; APPROVE not treated as merge.
