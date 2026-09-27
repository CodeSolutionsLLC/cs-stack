---
name: Starship create pr
description: >-
  Use when opening a pull request for coding work — PR body, CI, human
  gate before request review/merge path. Public Soft-pull twin for cs-stack (scrubbed).
---
# Starship create pr

**Public Soft-pull twin** (scrubbed for CodeSolutionsLLC/cs-stack). Generic git/GitHub/CI patterns for any Grok Bot user.

Open clear, reviewable pull requests for coding work: correct branch state, accurate PR body, CI awareness, and a **human gate before request-review / merge**. Complements [Starship commit](../starship-commit/SKILL.md) (commits before the PR) and [Starship issue workflow](../starship-issue-workflow/SKILL.md) (how issues are worked). Merge only through [Starship computer gates](../starship-computer-gates/SKILL.md) + [Verify before merge](../verify/SKILL.md).

## When to use

- Opening or updating a PR for a work leaf / issue
- Drafting a PR title and body for human review
- After commits are on a feature branch and you need a reviewable PR
- Before requesting reviewers or asking for merge

## Hard rules

- [Human gate every step](../../principles/human-gate.md) — **before requesting review and before any merge path**. Show title, body, base/head, CI status summary; get explicit OK. Silence ≠ OK. Creating a **draft** PR after secret/diff review is allowed when session policy permits; promoting to ready-for-review and merge still need human OK.
- **No secrets** — never include credentials, tokens, `.env` contents, private keys, vault IDs, cookies, or customer PII in PR titles, bodies, comments, or diffs. Sanitize error output.
- [Starship computer gates](../starship-computer-gates/SKILL.md) — no CI bypass; never merge on red/missing checks; never `--no-verify` / force-merge / skip required status checks.
- Prefer Local CI env install + [Starship computer CI delta baseline](../starship-computer-ci-delta-baseline/SKILL.md) before treating the PR as ready for review.
- Soft-fail if coordination infra is down — still open/update the draft PR when the branch is owned by this session; do not invent ownership of another session's PR or force-push their branch.
- Never create a PR directly from `main` / `master`.

## Prerequisites

```bash
gh --version
gh auth status
git status
git branch --show-current
```

Commits should already follow [Starship commit](../starship-commit/SKILL.md). If nothing is committed yet, commit first — do **not** empty-commit solely to unblock `gh pr create`.

## PR process

### 1. Verify branch state

Ensure you are on the correct branch and it is not the default branch.

```bash
git branch --show-current   # must NOT be main/master
git status                  # expected files only; unexpected mods → investigate
git fetch origin
```

**Never** open a PR from `main`/`master`. Create a feature branch first if needed.

**Branch naming.** Prefer coordinator/maintainer / repo leaf conventions (e.g. `feat/…`, `fix/…`, `leaf/<issue>-…`). Avoid colliding with other sessions' branches. Do **not** hard-require foreign session-ID prefixes or private box-only branch scripts.

**Worktree when contended.** If the branch was not already created in a worktree by [Starship issue workflow](../starship-issue-workflow/SKILL.md), and the clone is shared, prefer an isolated worktree over in-place `git checkout -b` so a parallel session cannot disturb the tree:

```bash
BRANCH="<repo-convention-branch-name>"
git worktree add -b "$BRANCH" "../wt-${ISSUE_NUMBER:-leaf}" origin/main && cd "../wt-${ISSUE_NUMBER:-leaf}"
```

Push and open the PR from the worktree as normal; remove the worktree at session closeout when [Starship session closeout](../starship-session-closeout/SKILL.md) is imported.

**`gh` quirks inside a worktree (cosmetic — do not panic):**

1. **`gh pr create` may abort with _"you must first push the current branch to a remote"_ even after a successful `git push -u`.** A worktree may not store the remote-tracking ref, so `gh` cannot see the upstream. Confirm the push landed, then pass refs explicitly:

   ```bash
   git ls-remote --heads origin "$BRANCH"          # proves the push landed
   gh pr create --head "$BRANCH" --base main ...   # explicit refs; no upstream lookup
   ```

2. **`gh pr merge --delete-branch` may print `fatal: 'main' is already used by worktree at …`.** That is `gh` trying to switch the *local* checkout after merging; **the merge already succeeded** if remote state says so. Prefer human-driven merge via merge/ship gates anyway. Verify:

   ```bash
   gh pr view <N> --json state,mergeCommit --jq '"\(.state) \(.mergeCommit.oid[0:10])"'
   ```

### 2. Push to remote (human gate when required)

Push with upstream tracking. When session/team policy requires operator OK before push (shared branches, production-adjacent repos), gate first per [Starship commit](../starship-commit/SKILL.md) step 7 / [Human gate every step](../../principles/human-gate.md).

```bash
git push -u origin "$(git branch --show-current)"
```

### 3. Analyze all changes

Review the full scope that will be in the PR — the description must accurately reflect every change.

```bash
git log origin/main..HEAD --oneline
git diff origin/main...HEAD --stat
git diff origin/main...HEAD
```

Scan the diff for secrets before drafting the body:

```bash
git diff origin/main...HEAD | grep -iE 'password|secret|token|api_key|private_key' || echo "Clean"
```

Real secrets → stop; scrub with a follow-up commit; never open the PR with secrets in the diff.

### 4. Draft PR title

Concise title (under 70 characters):

- Imperative mood: "Add", "Fix", "Update", "Remove"
- Include scope/area
- Be specific: "Add user authentication" not "Update code"
- Optional issue ref in title when repo convention prefers it

### 5. Draft PR body

Use this standard shape (adapt sections to the change):

```markdown
## Summary

- [1-3 bullet points describing what this PR does and why]

## Changes

- [Detailed list of what was changed, added, or removed]

## Test Plan

- [ ] [How to verify — local CI commands, manual steps, or screenshots]

## Related Issues

- Closes #N (or Refs: #N if not closing)
```

**No secrets** in Summary/Changes/Test Plan. Prefer linking to sanitized logs over pasting credential-bearing output.

Show the operator the proposed title + full body + base/head before creating (or before marking ready / requesting review — see hard rules).

### 6. Create the PR

Prefer draft first when further work or human review of the body is expected:

```bash
gh pr create --draft --title "WIP: …" --body "$(cat <<'EOF'
## Summary
- …

## Changes
- …

## Test Plan
- [ ] …

## Related Issues
- Refs: #N
EOF
)"
```

Or with explicit head/base (worktree-safe):

```bash
gh pr create --draft --head "$BRANCH" --base main \
  --title "…" --body-file /tmp/pr-body.md
```

Ready (non-draft) create is fine when the leaf is complete **and** human OK was given on title/body — still do **not** request reviewers or merge until CI + merge/ship gates + human OK.

**Troubleshooting "No commits between main and HEAD"**

If `gh pr create` fails with:

```text
pull request create failed: GraphQL: No commits between main and <branch>
```

the correct response is to **make a real commit, not `git commit --allow-empty`**. Empty-commit workarounds pollute history. Per [Starship issue workflow](../starship-issue-workflow/SKILL.md) / [Starship commit](../starship-commit/SKILL.md): first real commit → push (gated when required) → `gh pr create`.

If you truly need an empty commit (e.g. trigger a CI rerun on an existing branch), follow starship-commit empty-commit rules: include a `Reason for empty commit:` line — never empty-commit solely to unblock PR create.

### 7. Post-creation — CI, then human gate before review/merge

After the PR exists:

```bash
gh pr view --json number,url,isDraft,statusCheckRollup
# or: gh pr checks <N>
```

1. **Confirm CI** — required checks green or in progress. Prefer local CI first (Local CI env install + delta baseline). Do not request review on known-red CI unless the operator explicitly wants eyes on a failing draft.
2. **Labels** — add repo-appropriate labels after human OK if material (`enhancement`, `bug`, `security`, `cat:*`, …).
3. **Human gate before request review** — show PR URL, title, body summary, CI rollup. Get explicit OK, then:

   ```bash
   gh pr ready <N>                    # if still draft
   gh pr edit <N> --add-reviewer …    # only after OK; do not invent reviewers
   ```

4. **Human gate before merge** — [Verify before merge](../verify/SKILL.md) when UI/user-facing; then [Starship computer gates](../starship-computer-gates/SKILL.md). **Human merge only.** Agents do not merge unless the maintainer explicitly overrides for that specific act.

```bash
# Verify outcome after a human merge (do not drive merge from this skill by default)
gh pr view <N> --json state,mergeCommit --jq '"\(.state) \(.mergeCommit.oid[0:10])"'
```

## Branch naming conventions (advisory)

| Pattern                | Use for          | Example                 |
| ---------------------- | ---------------- | ----------------------- |
| `feat/description`     | New features     | `feat/user-auth`        |
| `fix/description`      | Bug fixes        | `fix/login-redirect`    |
| `docs/description`     | Documentation    | `docs/api-reference`    |
| `refactor/description` | Code refactoring | `refactor/db-queries`   |
| `chore/description`    | Maintenance      | `chore/update-deps`     |
| `security/description` | Security fixes   | `security/xss-sanitize` |

Prefer coordinator/maintainer / leaf naming when the repo already has a stronger convention.

## PR size guidelines

| Size   | Files | Lines changed | Guidance                            |
| ------ | ----- | ------------- | ----------------------------------- |
| Small  | 1–3   | < 100         | Ideal — easy to review              |
| Medium | 4–10  | 100–400       | Acceptable — provide a good summary |
| Large  | 10+   | 400+          | Consider splitting into stacked PRs |

If too large:

1. Extract independent changes into separate PRs
2. Use stacked PRs where later PRs depend on earlier ones
3. Prefix dependent PRs: "Part 2/3: …"

## Security considerations

- Never include secrets, credentials, or `.env` files in PR diffs or bodies
- For security-sensitive PRs, note the security impact in the summary (no exploit detail)
- Request security review (after human OK) for authn/authz/data-handling changes
- Never paste live tokens into review comments or "how to test" steps

## NIST alignment (advisory)

- **CM-3 (Change Control)**: PRs provide structured change review and approval
- **AC-3 (Access Enforcement)**: CODEOWNERS / required reviewers enforce authorization — honor them; do not bypass

## Anti-patterns

- Opening a PR from `main`/`master`
- Empty commits solely to unblock `gh pr create`
- Secrets in title, body, comments, or diff
- Requesting review or merging without human OK
- Merging on red/missing CI or skipping merge/ship gates
- Hard-requiring foreign session IDs, slash commands, or private box-only hooks
- Inventing reviewers / force-pushing another session's branch

## Related

- [Starship commit](../starship-commit/SKILL.md)
- [Starship issue workflow](../starship-issue-workflow/SKILL.md)
- [Starship computer gates](../starship-computer-gates/SKILL.md)
- Local CI env install
- [Starship computer CI delta baseline](../starship-computer-ci-delta-baseline/SKILL.md)
- [Verify before merge](../verify/SKILL.md)
- [Human gate every step](../../principles/human-gate.md)
- [Starship session closeout](../starship-session-closeout/SKILL.md) (when imported — worktree cleanup)
- [Starship github issue](../starship-github-issue/SKILL.md)

## Success proof

Branch not default; commits real (no empty-only unblock); title/body accurate and secret-clean; draft or ready PR opened with correct base/head; CI checked; human OK before request-review and before merge; merge/ship gates respected.
