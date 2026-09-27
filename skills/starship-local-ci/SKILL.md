---
name: Starship local ci
description: >-
  Use when running CI checks locally before push — lint, tests, security scans;
  complements local-ci-env-install. Public Soft-pull twin for cs-stack (scrubbed).
---
# Starship local ci

**Public Soft-pull twin** (scrubbed for CodeSolutionsLLC/cs-stack). Generic git/GitHub/CI patterns for any Grok Bot user.

Run CI verification **locally before push** so remote workers are not burned on avoidable red. Complements Local CI env install (**this skill = run checks**; **that skill = set up / smoke the env**). Scope work with [Starship computer CI delta baseline](../starship-computer-ci-delta-baseline/SKILL.md); never bypass [Starship computer gates](../starship-computer-gates/SKILL.md).

## When to use

- Before every `git push` on a PR-bound or shared branch
- Before opening or promoting a pull request
- After changes that could affect lint, tests, security, or CI config
- When the operator asks to verify locally / prove green before remote CI
- After Local CI env install reports `ready` (or you already have a documented local runner)

**Do not use this skill to install toolchains** — that is Local CI env install. If the env is missing or blocked, stop and run (or hand off to) env-install first; record `local-ci-env:{repo}: blocked` reason rather than inventing remote burn.

## Hard rules

- [Human gate every step](../../principles/human-gate.md) — **before push** when session/team policy requires operator OK; show the Local CI Report summary. Silence ≠ OK. Running local checks themselves does not need a gate; declaring READY TO PUSH / requesting remote CI / merge path does when policy says so.
- **No secrets** — never print, log, or paste credentials, tokens, `.env` contents, private keys, vault IDs, cookies, or customer PII into the Local CI Report, chat, or commit messages. Sanitize scanner output before sharing.
- [Starship computer gates](../starship-computer-gates/SKILL.md) — no CI bypass; never `--no-verify` / skip required checks / push hoping remote will pass.
- Prefer [Starship computer CI delta baseline](../starship-computer-ci-delta-baseline/SKILL.md): valid baseline → **delta + touched systems** locally first; full suite when baseline invalid, CI config changed, or drill/ship demands it.
- Env must be ready via Local CI env install (or equivalent documented smoke). Soft-fail if coordination / scanner infra is down — still run language lint+test with what is on-box; note skipped phases; do not invent green for skipped blocking phases.

## Project type auto-detection

Detect project type(s) from config files in the repo root (or documented monorepo package roots). Run checks for **all** detected types.

| Config file | Project type | Test runner | Linter | Formatter |
| ----------- | ------------ | ----------- | ------ | --------- |
| `pyproject.toml` / `setup.py` | Python | `pytest` | `ruff check`, `mypy` | `ruff format --check` |
| `package.json` | Node/TypeScript | `npm test` / `vitest` | `eslint`, `tsc --noEmit` | `prettier --check` |
| `Cargo.toml` | Rust | `cargo test` | `cargo clippy` | `cargo fmt --check` |
| `go.mod` | Go | `go test ./...` | `golangci-lint` | `gofmt -l` |
| `*.sh` (no other config) | Shell/Bash | `bats` (if `tests/` exists) | `shellcheck` | N/A |
| `*.yml` in `.github/workflows/` | CI/Config | YAML lint | `yamllint` | N/A |

Prefer the **same commands** the repo’s CI / Makefile / `package.json` scripts already document when they exist (align with env-install smoke). Fall back to the table only when the repo has no documented local target.

## Scope: full vs delta

Before Phase 1, decide scope (show on the report):

1. **Delta + touched (default)** — when [Starship computer CI delta baseline](../starship-computer-ci-delta-baseline/SKILL.md) baseline fingerprint is still valid: lint/test/scan only changed paths + systems touched by the delta.
2. **Full local suite** — baseline missing/invalid, CI config changed, pre-merge/ship drill, or operator requested full.
3. **Quick mode** — docs/comment/whitespace/single-file fix with existing coverage (see below); still secret-scan when any non-doc path changed.

Never use delta scope to skip a system the delta **did** touch.

## 5-phase local CI process

### Phase 1: Lint and format

Run language-appropriate linters and formatters in **check** mode (do not auto-rewrite unless the operator asked for format fixes).

```bash
# Python (example — prefer repo scripts when present)
ruff check . && ruff format --check . && mypy . 2>/dev/null

# Shell
find . -name "*.sh" -not -path "./.git/*" -exec shellcheck -x {} +

# TypeScript/Node
npx eslint . && npx tsc --noEmit
```

**Blocking**: Fix all lint **errors** before proceeding. Warnings are acceptable unless repo policy treats them as errors.

### Phase 2: Test suite

Run the project’s test suite (delta-scoped when baseline allows). Prefer coverage flags the repo already uses.

```bash
# Python
pytest --tb=short

# Shell (BATS)
cd tests && bats *.bats

# Node
npm test

# Go
go test ./...
```

**Blocking**: All in-scope tests must pass. Do not push with failing tests.

### Phase 3: Security scan

Run first-party / on-box security tools **when available** (paths vary by repo; common patterns: `./tools/`, `scripts/`, or commands recorded by env-install). Human-skip with an explicit note if tools are absent — do **not** invent PASS.

```bash
TOOLS_DIR="./tools"

# Secret scanner (blocking when present)
if [[ -f "$TOOLS_DIR/secret-scanner.sh" ]]; then
    bash "$TOOLS_DIR/secret-scanner.sh" --fail .
fi

# Code pattern scanner (blocking when present)
if [[ -f "$TOOLS_DIR/code-pattern-scanner.sh" ]]; then
    bash "$TOOLS_DIR/code-pattern-scanner.sh" --fail .
fi

# Spell checker (advisory when present)
if [[ -f "$TOOLS_DIR/spell-checker.sh" ]]; then
    bash "$TOOLS_DIR/spell-checker.sh" .
fi
```

Always do a lightweight staged/working-tree secret grep before declaring READY TO PUSH (same spirit as [Starship commit](../starship-commit/SKILL.md)):

```bash
git diff --cached | grep -iE 'password|secret|token|api_key|private_key' || echo "cached: clean"
git diff | grep -iE 'password|secret|token|api_key|private_key' || echo "workdir: clean"
```

**Blocking**: Secret scanner and code-pattern scanner failures must be fixed. Spell checker is advisory. False positives → confirm with operator before push; real secrets → scrub, never push.

### Phase 4: Compliance scan (if scanners installed)

```bash
for scanner in iso-scan iso42001-scan cis-scan soc2-scan pci-scan hipaa-scan asvs-scan; do
    if command -v "$scanner" &>/dev/null; then
        echo "Running: $scanner"
        $scanner --format text . 2>&1 | tail -5
    fi
done
```

**Non-blocking**: Informational. Note scores; do not block unless score **decreased** from the last recorded run for this repo (or operator policy says otherwise). Human-skip if none installed.

### Phase 5: Verify and report

Produce a Local CI Report (no secrets in the text):

```text
Local CI Report
===============
Repo: {name}
Scope: delta+touched | full | quick
Baseline: {id|none} (valid? yes/no)
Project types: Python, Shell
Date: {America/Chicago local}
Branch: $(git branch --show-current)

Phase 1 - Lint/Format: PASS|FAIL|SKIP(reason)
Phase 2 - Tests:       PASS|FAIL|SKIP(reason) ({n} passed, {m} failed)
Phase 3 - Security:    PASS|FAIL|SKIP(reason) ({findings} findings)
Phase 4 - Compliance:  INFO|SKIP ({scores or n/a})
Phase 5 - Summary:     READY TO PUSH | NOT READY

{one-line next step}
```

**READY TO PUSH** only when every **blocking** in-scope phase is PASS (or explicitly N/A with operator-accepted reason). Then human-gate push when policy/team requires it.

## Quick mode

For small, low-risk changes, run a quick subset (still record scope=`quick` on the report):

```bash
# Example Python quick: lint + test only
ruff check . && pytest --tb=line -q
```

Use quick mode for:

- Documentation-only changes (still skim for accidental secrets in docs)
- Comment or whitespace-only changes
- Single-file fixes with existing test coverage

Do **not** use quick mode when CI config, auth, crypto, dependency locks, or shared contracts changed — use delta+touched or full.

## CI workflow alignment

| Local phase | Remote CI job (typical) | Priority |
| ----------- | ----------------------- | -------- |
| Phase 1: Lint | config-validation / lint job | Must match |
| Phase 2: Tests | test job | Must match |
| Phase 3: Security | security-scan workflow | Must match when tools exist |
| Phase 4: Compliance | compliance / iso\*-scan workflows | Informational |

If local PASS but remote FAIL: investigate the delta (env, versions, path filters, required checks), update the **documented local commands** via Local CI env install / this skill’s checks, and re-baseline per [Starship computer CI delta baseline](../starship-computer-ci-delta-baseline/SKILL.md) when CI config itself moved.

## Guidelines

- **Always run before push** — primary defense against wasted CI budget
- **Fix issues locally** — never push hoping remote CI will pass
- **Batch changes** — run local CI once for a logical batch, not necessarily per micro-commit (commits still follow [Starship commit](../starship-commit/SKILL.md))
- **Skip compliance for speed** when the change is unrelated — Phase 4 optional
- **Update checks** when remote catches what local missed
- **Env first** — blocked env → env-install, not silent remote

## Anti-patterns

- Skipping local because remote exists
- Claiming READY TO PUSH without running blocking phases (or with FAIL)
- Using delta scope to skip systems the change touched
- Hard-requiring foreign slash commands (foreign local-ci / verification-loop slash cmds) or private `~/.local-tooling/` paths
- Printing secrets / raw `.env` into the report or chat
- `--no-verify` / bypassing hooks to “make local green”
- Overwriting or renaming Local CI env install — that skill remains the env installer; this skill id is **`starship-local-ci`**

## Related

- Local CI env install — set up / smoke the local runner (**complement; do not merge**)
- [Starship computer CI delta baseline](../starship-computer-ci-delta-baseline/SKILL.md) — baseline + delta+touched scope
- [Starship computer gates](../starship-computer-gates/SKILL.md) — no bypass before merge/ship
- [Human gate every step](../../principles/human-gate.md)
- [Starship commit](../starship-commit/SKILL.md)
- [Starship create pr](../starship-create-pr/SKILL.md) (when imported)
- [Verify before merge](../verify/SKILL.md)
- [Starship session closeout](../starship-session-closeout/SKILL.md) (when imported)

## Success proof

Env ready (or blocked with reason); scope chosen (delta|full|quick) with baseline note; blocking phases PASS; Local CI Report produced without secrets; push only after human OK when required; skill id remains `starship-local-ci` (env installer untouched).
