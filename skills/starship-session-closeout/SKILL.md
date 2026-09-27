---
name: Starship session closeout
description: >-
  Use when ending a coding or ops session — handoff notes, tracker refresh, open
  threads; no secrets. Public Soft-pull twin for cs-stack (scrubbed).
---
# Starship session closeout

**Public Soft-pull twin** (scrubbed for CodeSolutionsLLC/cs-stack). Generic git/GitHub/CI patterns for any Grok Bot user.

End-of-session hygiene so work is not lost: handoff notes, issue updates, upstream findings, branch/worktree cleanup, ownership release, tracker refresh, next-session recommendation, and a short status report. Complements [Starship issue workflow](../starship-issue-workflow/SKILL.md) (how issues are worked) and [Starship issue tracker](../starship-issue-tracker/SKILL.md) (per-repo index).

## When to use

- At the end of any coding or ops session
- Before context window approaches capacity
- When switching between repos mid-session
- When the operator requests a session wrap-up

## Hard rules

- [Human gate every step](../../principles/human-gate.md) — **before** destructive cleanup (`git branch -D`, worktree remove), material tracker/board body edits, vault re-encrypt, or any push/merge leftover; silence ≠ OK
- **No secrets** — never put tokens, credentials, vault plaintext, cookies, customer PII, or private paths-with-contents into handoff notes, issue comments, tracker bodies, board rows, or the status report. Sanitize error output
- [Starship computer gates](../starship-computer-gates/SKILL.md) — no CI bypass; never leave a red PR as "done"
- Soft-fail if coordination / board / tracker infra is down or unbound (`not-yet-bound`) — still finish local hygiene and handoff notes; do not invent ownership or invent a board bind
- Do **not** hard-require foreign slash commands, `~/.local-tooling/` paths, or private box-only hooks

## Closeout process

### Step 1: Handoff notes (durable memory)

Record decisions, findings, and patterns from this session that the **next** session will need. Prefer coordinator / agent memory and ownership handoff notes when your team uses them; otherwise a short durable note the operator can find.

**What to save:**

- Non-obvious decisions (and why)
- New patterns or conventions established
- Discovered bugs or issues not yet addressed
- Cross-repo impacts identified
- Operator preferences or feedback received

**What NOT to save:**

- Ephemeral task state (use issues instead)
- Code patterns derivable from the codebase
- Information already in repo docs / README
- Secrets, tokens, vault values, or credential paths with contents

**Symptom-first keys.** For anything that cost this session time, record the **symptom** the next session will see — not only the eventual cause:

- the **literal error text**, quoted — `"Error: Encryption failed"`, `"command not found"`
- the **failing command** and the tool/script name
- what it **looked like** before you knew — "read as a product bug", "reported PASS while broken"

Never shorten an existing match key to make room. Add your entry; if the index is oversized, that is a dedicated memory-maintenance pass later — not something to fix by deleting other sessions' retrieval keys under time pressure.

### Step 2: Handoff accuracy pass

After writing new notes, review them for **accuracy only** — do not compact or trim for size mid-closeout.

- Verify counts / structures still match reality
- Resolve any PR/issue numbers referenced in new entries — `gh pr view <N>` / `gh issue view <N>` (or `--repo <owner>/<repo>`) for each; correct or drop bad refs
- Correct entries this session proved **wrong**; delete only what is now **false**

⛔ **Do not trim for size here.** Adding and consolidating are different jobs. Trimming under time pressure shortens match keys and silently destroys retrieval. Oversize → schedule a dedicated maintenance pass; never drop links or shorten keys in closeout.

If this session made substantial repo changes (adds/removes, renames, major refactors), consider a separate repo-hygiene pass later — that is repo-scoped, not this session-scoped step.

### Step 3: Update issues

Comment on or close issues affected by this session's work.

```bash
# Issues referenced in commits this session (adjust window as needed)
git log --oneline --since="$(date -d '4 hours ago' +%Y-%m-%dT%H:%M:%S)" | grep -oE '#[0-9]+' || true
```

- If work is complete: close via commit/PR keyword after push — **never** `gh issue close` before the fix is committed and pushed
- If work is partial: post a checkpoint comment (format below)
- Prefer [Starship github issue](../starship-github-issue/SKILL.md) conventions when creating follow-ups

#### Step 3a: Verify final state of every issue touched — after merge

Auto-close is a text parser, not intent. Confirm what landed:

```bash
for n in <issue numbers>; do
  gh issue view "$n" --json number,state --jq '"#\(.number) \(.state)"'
done
```

Reconcile both directions:

- **Closed but shouldn't be** → reopen and say why. GitHub matches `(close[sd]?|fix(e[sd])?|resolve[sd]?)` next to `#N` and does **not** understand negation — e.g. "filed before this closes: #223" can close #223. Lists often bind only the first ref
- **Open but should be closed** → keyword missing/misspelled; close now citing the merge commit
- **Cross-repo refs count** — `Closes <owner>/<repo>#N` does auto-close across repositories; check qualified refs, not just bare `#N`

Do this **after** the merge lands, not only before.

**Checkpoint comment format** (partial work):

```markdown
## Session Checkpoint

**Completed:**

- [list]

**In Progress:**

- [list]

**Remaining:**

- [list]

**Resume From:**

- [specific next step]
```

**No secrets** in checkpoint bodies.

### Step 4: Report upstream

If findings affect other repos, file issues there (after human OK when material):

```bash
gh issue create --repo <owner>/<repo> \
  --title "Finding from <source-repo> session" \
  --body-file <finding.md>
```

Detect owner/repo with `gh repo view --json nameWithOwner` — do not hardcode silent defaults. Ask the operator for cross-repo targets when unclear.

**When to report upstream:**

- Scanner findings that affect downstream repos
- Dependency updates needed elsewhere
- Patterns that should apply org-wide
- Security issues in shared libraries (sanitize; no PoCs or live secrets in the body)

### Step 5: Branch and worktree hygiene

After commits/merges this session, verify the working tree and prune stale refs. Run in every repo touched.

```bash
git status --short
git fetch --all --prune
git branch -vv | grep ': gone]' || true
git worktree list
```

**Cleanup rules:**

- **Untracked files that must never be committed** (audit logs, session state, local artifacts): add a targeted `.gitignore` line — don't bury the fix in an unrelated change
- **Stale local branches with `[upstream: gone]`**: typically squash-merged. Confirm merged (`gh pr view <N>` → `MERGED`), then `git branch -D` only after [Human gate every step](../../principles/human-gate.md). Unattended: list candidates in the Step 9 report and stop
- **Never force-delete without confirming merge** — `[upstream: gone]` only proves the remote ref is gone
- **Never delete a `staged/**` branch** (local or remote) — that namespace is a review-ledger staging area; disposition owns deletion, not routine closeout

**Worktree cleanup** (when this session used an isolated worktree — preferred under contention per [Starship commit](../starship-commit/SKILL.md) / [Starship issue workflow](../starship-issue-workflow/SKILL.md)):

```bash
# From the MAIN clone, after confirming the PR merged
git worktree remove <path-to-session-worktree>
git worktree prune
```

- Confirm merge before remove (`gh pr view <N>` → `MERGED`)
- `git worktree remove` refuses dirty trees — investigate; do not `--force` away uncommitted work
- Unattended: list `git worktree list` in the status report and stop for confirmation

### Step 5a: Release session ownership (coordinate)

Release claims so issues/files this session owned are free for the next worker, and the active-session roster reflects reality.

Prefer ownership handoff + coordinator ops coordination when your team uses them:

- Check in any files still claimed
- End / close this session's ownership card so the roster drops it
- Verify active sessions no longer list this session

If coordination tools/network unavailable: soft-fail, note it in Step 9, still finish local hygiene. A claim left open after closeout is a crash signal — a future session may reclaim it.

Do **not** invent ownership of another session's files or force co-own without explicit operator OK.

### Step 5b: Session cache / scratch review (advisory)

Review durable tool caches this session touched — **review, never blind-nuke**.

- Reclaim per-item against reasonable TTLs (system/CVE-sensitive shorter; first-party longer)
- Never `rm -rf` a whole shared cache base; operate only on per-tool subdirs this session owns
- Preserve agent / coordinator state dirs unless the operator explicitly OK's removal
- Unattended / no TTY: list stale candidates in Step 9; leave removal to the operator

Orphan dirs with no owner tool are a separate hygiene pass — note them; do not delete here.

### Step 5c: Temp-file cleanup (this session only)

Reclaim `/tmp` (and similar) files **this session created**. Prefer a session manifest or explicit path list you recorded while working — do **not** pattern-sweep all of `/tmp` (that can delete other sessions' files).

```bash
# Example: remove only paths you recorded for THIS session
# Sensitive taxonomy → shred -u; otherwise rm -f
# Never delete paths outside the session's recorded set
```

**Rules:**

- Manifest- or attribution-scoped — only this session's files
- Sensitive purpose (`auth|secret|token|credential|passphrase|…`) → `shred -u`, not plain `rm`
- Tampered / out-of-grammar paths → skip + log; never delete
- Report counts in Step 9 (`cleaned=N sensitive=M`)

### Step 5d: Encrypted-document sweep — HARD GATE

**No session ends with secret material sitting in plaintext on disk.**

⚠️ **`git status` cannot catch this.** Plaintext vault / breakfix / drill-log forms are typically `.gitignore`d by design, so a decrypted vault leaves the working tree looking clean.

In **every** repo touched this session, look for decrypted vendor-only secrets (names vary by repo; common patterns include breakfix / vault values / fire-drill logs **without** their encrypted sibling extension):

```bash
# Example discovery — adapt paths to the repos you touched; metadata only
find . -path '*/docs/vendor-only/*' \
  \( -name 'BREAKFIX.md' -o -name 'BREAKFIX-VALUES.md' -o -name 'fire-drill-log.md' \) \
  -not -name '*.age' 2>/dev/null
```

Any hit:

1. **Confirm the encrypted sibling is current** — compare mtimes. If plaintext is **newer** than `.age` (or equivalent), re-encrypt first (operator-driven; human gate) — shredding would destroy work
2. **Confirm encrypted form is committed AND pushed** — `git diff --quiet origin/main -- <file>.age` (or default branch). Encrypted-but-unpushed is one disk failure from lost credentials
3. **Then remove plaintext with `shred -u`**, never plain `rm`

**Do not read the plaintext** to check any of this. Metadata only — `stat`, `ls`, `git log`, `git diff --quiet`. Agents never need vault values for closeout.

**Distinguish tracked structure docs** (templates / indexes with no values) from decrypted secrets: if `git ls-files --error-unmatch <file>` succeeds, it is a tracked doc, not a decrypted secret.

Report on the Step 9 line (`encrypted-docs: clean` or files actioned). If a fire drill ran this session, say so explicitly — highest risk.

### Step 6: Update documentation

Review and update docs **directly affected** by this session's work only.

Common checks:

- `README.md` — catalogs, badges, compliance tables
- Breakfix / vault **structure** docs — only if credentials/encryption process changed (never paste values)
- `CHANGELOG.md` / release notes — if applicable
- `docs/` — standards, processes, schedules

Do not proactively rewrite unrelated documentation.

### Step 7: Org-wide PR sweep (penultimate, advisory)

Before finalizing, if a org-PR-sweep skill / coordinator board digest is imported and last run was ≥24h ago:

- Report FRESH / WARN (3–7d) / PROMOTE (7+d) tiers for stale open PRs
- Promote long-lived `@me`-authored PRs to tracking issues (URL + final SHA + branch + author) when that is the team convention
- Respect concurrent sessions via the active-session roster — do not steal another teammate's PR
- Operator may skip for scoped sessions

If no sweep skill is imported or coordination is unbound: skip and note in Step 9.

### Step 7.5: Tracker sync — per-repo tracker + central board

Sync so the next session sees current priority. Runs before Step 8 so the recommendation reflects a fresh board.

#### 7.5a: Scope completeness gate

Every remaining or newly discovered piece of work from this session MUST have an open issue before syncing — nothing tracked only in comments, memory, or a checkpoint. File missing ones now via [Starship github issue](../starship-github-issue/SKILL.md) (with `cat:*` when your taxonomy applies), then proceed.

#### 7.5b: Per-repo tracker update

For each repo touched, update its single `tracker`-labeled issue per [Starship issue tracker](../starship-issue-tracker/SKILL.md): index new issues, check off closed ones, re-order priority sections to stay consistent with the central board where this repo's items appear. Human gate before material body edits.

#### 7.5c: Central board update (when bound)

If the coordinator has an active private ops tracker / `deployment-tracker` board bind:

```bash
# Locate the board (exactly one org-wide when bound) — adapt owner/repo from bind, do not invent
BOARD=$(gh issue list --repo <ops-tracker-owner>/<ops-tracker-repo> \
  --label deployment-tracker --state open --json number --jq '.[0].number')
```

If no board / unbound / `not-yet-bound`: note in Step 9 and skip (creating/binding the board is a deliberate operator action, not a closeout side effect).

Otherwise:

- Move rows completed this session to _Recently completed_
- Add newly filed deployment-significant issues with proposed rank + `cat:*`
- Refresh `in-work` flags from the active-session roster; clear stale ones
- Update `blocked (...)` refs that cleared; justify any re-rank in Notes
- Bump the `Last synced` line

**Edit-race guard (mandatory):** fetch the body immediately before editing; make surgical row edits (never regenerate the whole table); re-fetch before writing; if `Last synced` moved, re-apply on the newer body — on a second collision, post your delta as a **comment** instead of overwriting.

**Churn guard:** if this session made no material changes (no commits, no issues opened/closed, no claims) and the board's `Last synced` is <24h old, skip 7.5c.

### Step 8: Next-session recommendation

Post a structured recommendation on the active tracker so the next session ([Starship issue workflow](../starship-issue-workflow/SKILL.md) or a human) can pick up without re-deriving priority.

#### Tracker resolution order

Use the first match:

1. Explicit override: closeout with `--tracker <N>` (or `--tracker <owner>/<repo>#<N>` for cross-repo)
2. Most recent open issue with the `tracker` label in the current working repo
3. Most recent open `tracker` in the fleet's designated central tracker repo (when configured) — never invent a hardcoded fallback
4. None found → skip; note in Step 9

#### Churn guard

Skip posting if **all** of:

- A prior `Next Session Recommendation` comment exists on the resolved tracker **and**
- It was posted <24 hours ago **and**
- This session made no material changes (no commits, no closed issues, no new upstream issues)

#### Comment format

Comment MUST start with a stable marker so consumers can match it:

````markdown
<!-- starship-session-closeout:v1 -->

## Next Session Recommendation

**Working repo:** `<owner>/<repo>`
**Posted:** `<YYYY-MM-DD HH:MM America/Chicago>`
**Session summary:** <1-line description>

### Top 3 next actions

1. `<repo>#<N>` — <short description>. **Why first:** <dependency or urgency>
2. `<repo>#<N>` — <short description>. **Why second:** …
3. `<repo>#<N>` — <short description>. **Why third:** …

### Blockers / dependencies

- <what is waiting on what>
- <external blocker with owner/ETA>

### Context carryover

- <key decision this session that must flow forward>
- <approach chosen / rejected and why>

### Suggested starting command

```
<concrete next step, e.g. starship-issue-workflow on #148 or gh pr checkout 182>
```
````

#### Rules

- **Repo scope** — name the working repo explicitly; consumers warn if cwd mismatches
- **Top 3 only** — prioritize ruthlessly; the tracker body holds the full list
- **Board-aligned** — top-3 must not contradict the central board just synced in 7.5c; divergent order → board re-rank with Notes, not a rogue recommendation
- **Concrete next steps** — emit the exact skill / `gh` command the next session should run
- **No secrets** — sanitize paths, tokens, error output

### Step 9: Final status report

Produce a brief session summary for the operator:

```text
Session Summary
===============
Branch: <branch>
Commits: <count>
Issues closed: #X, #Y
Issues updated: #Z (checkpoint posted)
Upstream issues created: <repo>#<num>
Encrypted docs: <clean | files actioned>   # Step 5d — say if a drill ran
Handoff notes: <yes/no>
Docs updated: <list>
Ownership release: <released | soft-fail — infra down | n/a>
Tracker sync: repo tracker(s) <list> | central board <repo>#<B> (synced) | skipped — churn guard | skipped — unbound/no board
Next-session recommendation: <tracker>#<N> (posted) | skipped — churn guard | skipped — no tracker
Pending items: <list or "none">
Tooling gaps: <missing vetted local-CI tools or "none">
Skill harvest: <candidate + tracking issue or "none">
```

**Tooling-gap check (soft).** If Local CI env install / delta baseline is imported, note any vetted-but-uninstalled tools. Non-blocking.

**Skill-harvest check (soft).** Did this session hand-derive or repeat (≥2×) a procedure worth codifying? If yes, file a tracking issue (proposal-only — skills change by reviewed PR, never a direct write into published skill trees) and record it on the `Skill harvest` line. "none" is normal; never hold up closeout.

## Guidelines

- **Never close issues before code is committed and pushed** — false audit trail
- **Be selective with handoff notes** — only what is useful across sessions
- **Keep upstream reports actionable** — specific recommendations, not just observations
- **Don't over-document** — only docs this session directly affected
- **Run this skill even for short sessions** — a 2-minute closeout prevents hours of lost context
- **Human gate destructive and material remote edits** — list candidates when unattended

## Anti-patterns

- Leaving vault / breakfix plaintext on disk because `git status` looked clean
- Trimming handoff indexes for size mid-closeout (destroys retrieval keys)
- Hard-requiring foreign slash commands, `~/.local-tooling/` paths, or private box-only session hooks
- Editing / binding the central deployment board when unbound or `not-yet-bound`
- Force-deleting branches or worktrees without merge confirmation + human OK
- Secrets in handoff notes, checkpoints, tracker/board text, or the status report
- Closing issues before push

## Related

- [Starship issue workflow](../starship-issue-workflow/SKILL.md) — consumes Step 8 recommendations; owns issue work phases
- [Starship issue tracker](../starship-issue-tracker/SKILL.md) — per-repo tracker format (Step 7.5b)
- [Starship github issue](../starship-github-issue/SKILL.md) — filing follow-ups / upstream
- [Starship commit](../starship-commit/SKILL.md) — worktree + commit hygiene before closeout
- [Starship create pr](../starship-create-pr/SKILL.md) — when imported
- [Human gate every step](../../principles/human-gate.md)
- [Starship computer gates](../starship-computer-gates/SKILL.md)
- Local CI env install
- [Verify before merge](../verify/SKILL.md)

## Success proof

Handoff notes saved without secrets; issues updated/verified after merge; ownership released or soft-failed honestly; encrypted-docs gate clean (or actioned); tracker/board refreshed when bound; next-session recommendation posted or churn-skipped with reason; Step 9 report complete.
