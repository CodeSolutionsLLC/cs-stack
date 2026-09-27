# Skills

First-party installable skills for cs-stack v0. Each package is a directory
with `SKILL.md` (YAML frontmatter: `name`, `description`). Skills operationalize
playbooks — they do not vendor upstream packs.

## Install

Use [`setup-cs-stack`](setup-cs-stack/SKILL.md) for first-run configure. Choose one
profile — `full`, `leaf`, or `careful-only` — and that skill installs only those
packages. Or clone this repository and copy/symlink `skills/<name>/` into this
Grok Bot’s skill library. Load only the packages you need.

## v0 index

| Skill | Package | Playbook |
|-------|---------|----------|
| setup-cs-stack | [setup-cs-stack/SKILL.md](setup-cs-stack/SKILL.md) | first-run configure |
| careful-mode | [careful-mode/SKILL.md](careful-mode/SKILL.md) | standing rigor (v0 loop) |
| investigate | [investigate/SKILL.md](investigate/SKILL.md) | [playbooks/investigate.md](../playbooks/investigate.md) |
| fix-bug | [fix-bug/SKILL.md](fix-bug/SKILL.md) | [playbooks/fix-bug.md](../playbooks/fix-bug.md) |
| ship-change | [ship-change/SKILL.md](ship-change/SKILL.md) | [playbooks/ship-change.md](../playbooks/ship-change.md) |
| verify | [verify/SKILL.md](verify/SKILL.md) | [playbooks/verify.md](../playbooks/verify.md) |
| review-pr | [review-pr/SKILL.md](review-pr/SKILL.md) | [playbooks/review-pr.md](../playbooks/review-pr.md) |

Prove-it and no-self-merge are binding: [principles/verify.md](../principles/verify.md),
[principles/no-self-merge.md](../principles/no-self-merge.md),
[principles/human-gate.md](../principles/human-gate.md).

See [docs/PLAYBOOK-V0.md](../docs/PLAYBOOK-V0.md).

## Soft pull (starship)

Public Soft-pull twins for git, GitHub, and CI work. Folder names are the
Soft pull ids. These packages sit beside the v0 index above; they do not
replace it. Only this set is included.

| Skill | Package | Purpose |
|-------|---------|---------|
| starship-commit | [starship-commit/SKILL.md](starship-commit/SKILL.md) | Git commits for coding work: message style, scope, no secrets; human gate before push when required |
| starship-computer-automation-loops | [starship-computer-automation-loops/SKILL.md](starship-computer-automation-loops/SKILL.md) | Coordinator tracks automation candidates, runs a human-approved learning loop, and stops workers before editing a loop |
| starship-computer-ci-delta-baseline | [starship-computer-ci-delta-baseline/SKILL.md](starship-computer-ci-delta-baseline/SKILL.md) | Scope CI to one heavy baseline, then delta plus touched systems; local first |
| starship-computer-gates | [starship-computer-gates/SKILL.md](starship-computer-gates/SKILL.md) | All-systems-green before merge or ship: necessity, tests, simulation, and a human gate |
| starship-computer-humans-onboard | [starship-computer-humans-onboard/SKILL.md](starship-computer-humans-onboard/SKILL.md) | Record who uses the workspace and confirm before clearing memory or connectors |
| starship-create-pr | [starship-create-pr/SKILL.md](starship-create-pr/SKILL.md) | Open a pull request with an accurate body, CI awareness, and a human gate before review or merge |
| starship-github-issue | [starship-github-issue/SKILL.md](starship-github-issue/SKILL.md) | Create or draft GitHub issues with team conventions, labels, and no secrets |
| starship-issue-alignment | [starship-issue-alignment/SKILL.md](starship-issue-alignment/SKILL.md) | Align an open-issue backlog: collapse duplicates, close verified-complete work, re-rank |
| starship-issue-tracker | [starship-issue-tracker/SKILL.md](starship-issue-tracker/SKILL.md) | Keep one per-repo tracking issue as the index of open issues |
| starship-issue-workflow | [starship-issue-workflow/SKILL.md](starship-issue-workflow/SKILL.md) | Work a GitHub issue end to end; human confirm before implement and before merge |
| starship-local-ci | [starship-local-ci/SKILL.md](starship-local-ci/SKILL.md) | Run lint, tests, and security scans locally before push |
| starship-pr-review | [starship-pr-review/SKILL.md](starship-pr-review/SKILL.md) | Review a pull request for correctness, CI, and security; human gate before approve or request-changes |
| starship-session-closeout | [starship-session-closeout/SKILL.md](starship-session-closeout/SKILL.md) | End a session with handoff notes, tracker refresh, and open threads; no secrets |
