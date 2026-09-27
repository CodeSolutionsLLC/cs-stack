---
name: Starship commit
description: >-
  Use when making git commits for coding work — message style, scope, no
  secrets in commits; human gate before push when required. Public Soft-pull twin for cs-stack (scrubbed).
---
# Starship commit

**Public Soft-pull twin** (scrubbed for CodeSolutionsLLC/cs-stack). Generic git/GitHub/CI patterns for any Grok Bot user.

Guide clear, conventional commits for coding work: selective staging, message style, no secrets in messages or diffs, human gate before push when required. Complements [Starship issue workflow](../starship-issue-workflow/SKILL.md) (how issues are worked) and local/CI gates before merge.

## When to use

- Committing code/config/docs for work leaf or ops work
- Splitting mixed changes into scoped commits
- Empty commits that need an explicit justification line
- Before push / PR when branch=PR or merge/ship gates apply

## Hard rules

- [Human gate every step](../../principles/human-gate.md) — **before push** when the session policy or merge/ship gates require operator OK; silence ≠ OK. Commits themselves may proceed after secret/diff review unless policy says otherwise.
- **No secrets** — never stage or commit `.env`, private keys, credentials, tokens, certificates, vault IDs, cookies, or customer PII. Never put secrets in commit **messages** or leave them in staged **diffs**.
- [Starship computer gates](../starship-computer-gates/SKILL.md) — no CI bypass; never `--no-verify` / `--no-gpg-sign` to skip hooks.
- Prefer Local CI env install (+ delta baseline when imported) before treating a commit as "done" for a PR-bound branch.
- Soft-fail if coordination infra is down — still commit locally with clean staging; do not invent ownership of another session's files.

## Conventional commit format

```text
type(scope): description

[optional body]

[optional footer]
```

### Type reference

| Type       | Use for                                                 |
| ---------- | ------------------------------------------------------- |
| `feat`     | New feature or capability                               |
| `fix`      | Bug fix                                                 |
| `docs`     | Documentation only                                      |
| `refactor` | Code change that neither fixes a bug nor adds a feature |
| `test`     | Adding or updating tests                                |
| `chore`    | Maintenance tasks, dependency updates                   |
| `ci`       | CI/CD pipeline changes                                  |
| `perf`     | Performance improvement                                 |
| `security` | Security fix or hardening                               |

## Commit process

### 1. Review changes

```bash
git status
git diff
git diff --cached
```

Identify logical groupings — if changes span multiple concerns, plan separate commits.

### 2. Determine commit strategy

- **Single commit**: all changes serve one purpose
- **Split commits**: unrelated concerns (feature + unrelated docs + config)

**Branch naming.** Prefer coordinator/maintainer / repo conventions for the leaf. Avoid colliding with other sessions' branches. Do **not** hard-require foreign session-ID prefixes or private box-only branch scripts.

**Worktree when contended.** In a shared clone, prefer an isolated worktree over in-place `git checkout -b` so a parallel session cannot move HEAD or bundle another session's `git add` into your commit. Opt out only when the clone is demonstrably uncontended. Clean up worktrees at session closeout when [Starship session closeout](../starship-session-closeout/SKILL.md) is imported.

**Splitting guide**:

| Signal                                 | Action                                                                      |
| -------------------------------------- | --------------------------------------------------------------------------- |
| Changes in unrelated directories       | Split by directory/concern                                                  |
| Mix of feature + test + docs           | One commit per category is acceptable; combining is fine if tightly coupled |
| Mix of refactor + behavior change      | Always split — refactors should be isolated                                 |
| Formatting/whitespace mixed with logic | Split — keep formatting commits separate                                    |

### 3. Stage selectively

Always stage specific files — **never** `git add -A` or `git add .`.

```bash
git add path/to/file1 path/to/file2
git add -p path/to/file   # hunks when a file has mixed changes
```

**Before staging, check for sensitive files**:

```bash
git status | grep -E '\.(env|key|pem|p12|credentials|secret)' || true
```

Never commit: `.env`, private keys, credentials, tokens, certificates, or `node_modules/`.

### 4. Write the commit message

```bash
git commit -m "$(cat <<'EOF'
type(scope): concise description of what and why

Optional body explaining:
- Why this change was necessary
- What approach was taken
- Any important trade-offs or decisions

Refs: #issue-number (if applicable)
EOF
)"
```

**Message rules**:

- Subject: imperative mood, no period, under 72 characters
- Body: wrap at 72 characters; explain *why*, not only *what*
- Reference issues with `Refs: #N`, `Resolves #N`, or `Closes #N` as appropriate
- **No secrets** in subject or body (tokens, paths to credential files with contents, customer data)

#### Empty commits (`--allow-empty`) — justification required

Empty commits are **allowed only when justified in the body** via a `Reason for empty commit:` line. Legitimate cases:

| Case                                             | Example justification                                                        |
| ------------------------------------------------ | ---------------------------------------------------------------------------- |
| Trigger CI rerun on a workflow-modifying PR      | `Reason for empty commit: trigger required-signatures CI rerun`              |
| Force a new commit SHA for signed-commits gating | `Reason for empty commit: rotate SHA for ruleset re-evaluation`              |
| Mark a branch as touched                         | `Reason for empty commit: branch ack — PR opens in follow-up commit`         |

```bash
git commit --allow-empty -m "$(cat <<'EOF'
chore(ci): trigger workflow rerun

Reason for empty commit: required-signatures rule needs a fresh signed
commit to re-evaluate on origin/main; the prior force-push did not
trigger the workflow.

Refs: #N
EOF
)"
```

**Banned:** `git commit --allow-empty` solely to satisfy `gh pr create`'s "no commits between main and HEAD" check. Correct flow per [Starship issue workflow](../starship-issue-workflow/SKILL.md): make a **real** first commit, push (after human gate when required), then open the draft PR. Prefer [Starship create pr](../starship-create-pr/SKILL.md) when imported.

### 5. Pre-commit verification

```bash
git diff --cached --name-only
git diff --cached | grep -iE 'password|secret|token|api_key|private_key' || echo "Clean"
```

If the grep hits a false positive (e.g. docs mentioning "token"), confirm with the operator before committing. Real secrets → unstage and scrub; never commit.

### 6. Create the commit

```bash
git commit -m "type(scope): description"
```

**Never** `--no-verify` to skip pre-commit hooks. If hooks fail, fix the underlying issue (or use local CI tooling from Local CI env install when hooks expect that env).

After committing:

```bash
git log --oneline -1
git status
```

### 7. Push and branch=PR (human gate when required)

Push only after [Human gate every step](../../principles/human-gate.md) when session/team policy requires it (shared branches, production-adjacent repos, or operator standing rules). Show: branch name, `git log` since divergence, and that the secret scan was clean.

After pushing, check that the working branch has an open or draft PR (branch=PR enforcement for non-exempt branches):

```bash
BRANCH=$(git rev-parse --abbrev-ref HEAD)
case "$BRANCH" in
  main|master|HEAD|scratch/*) ;;  # exempt
  *)
    if [ "$(gh pr list --head "$BRANCH" --json number --jq 'length')" = "0" ]; then
      echo "No PR for $BRANCH. After human OK + push, open one:"
      echo "  git push -u origin $BRANCH"
      echo "  gh pr create --draft --title 'WIP: ...'"
    fi
    ;;
esac
```

**First commit on a new branch:** no PR yet is expected. Push (gated) and open the draft — do **not** preemptively empty-commit for `gh pr create`.

`scratch/*` branches are exempt (exploratory; no PR required).

## Security rules

- **Never** `git add -A` or `git add .` — stage specific paths
- **Never** `--no-verify` or `--no-gpg-sign` to bypass hooks
- **Never** `--force` push to shared branches (`main` / protected)
- **Never** commit files matching: `.env`, `*.key`, `*.pem`, `credentials.*`, `*.secret`
- **Always** review `git diff --cached` before committing
- **Always** let pre-commit hooks run to completion
- **Always** human-gate push when policy/team requires it

## NIST alignment (advisory)

- **CM-3 (Change Control)**: conventional format provides structured change records
- **AU-10 (Non-repudiation)**: commit authorship establishes accountability

## Anti-patterns

- Staging everything with `git add -A` / `git add .`
- Secrets in commit messages or staged diffs
- Skipping hooks / force-pushing protected branches
- Empty commits without `Reason for empty commit:`
- Empty commits only to unblock `gh pr create`
- Hard-requiring foreign session IDs, slash commands, or private box-only hooks
- Pushing without human OK when gates require it

## Related

- [Starship issue workflow](../starship-issue-workflow/SKILL.md)
- [Starship computer gates](../starship-computer-gates/SKILL.md)
- Local CI env install
- [Human gate every step](../../principles/human-gate.md)
- [Starship create pr](../starship-create-pr/SKILL.md) (when imported)
- [Verify before merge](../verify/SKILL.md)
- [Starship session closeout](../starship-session-closeout/SKILL.md) (when imported — worktree cleanup)
- [Starship github issue](../starship-github-issue/SKILL.md)

## Success proof

Staged set reviewed and secret-clean; conventional message (empty commits justified); hooks allowed to run; push only after human OK when required; branch has or will immediately get a draft PR when non-exempt.
