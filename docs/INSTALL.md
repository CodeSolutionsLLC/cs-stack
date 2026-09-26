# Install cs-stack

Public buyer / importer path: clone this repository, run first-run configure,
load the skills you need, then use the verify → review loop before anything
lands on `main`.

This guide is for **Grok Bot** (and other agent skill libraries). It does not
require a marketplace package.

## 1. Clone

```bash
git clone https://github.com/CodeSolutionsLLC/cs-stack.git
cd cs-stack
```

Confirm the tree has `playbooks/`, `principles/`, `skills/`, and `NOTICE`.

## 2. First-run configure

Follow [skills/setup-cs-stack/SKILL.md](../skills/setup-cs-stack/SKILL.md).

That skill is **idempotent**. It copies each `skills/<name>/SKILL.md` into the
host skill library your agent already uses. Optional models/roles config is
fail-open (skipped if absent). It does not write secrets.

Re-run after pulling updates; a second pass should skip unchanged packages.

### Grok Bot importer

Pitfalls for Boot and contest clones, and for any-user environments.
Installing cs-stack is optional. **Skip is OK.**

The clone may need GitHub auth. Paste or copy skills into **this bot’s**
skill library only.

On Windows, use Git Bash, or another shell where `git` is on `PATH`.
[setup-cs-stack](../skills/setup-cs-stack/SKILL.md) copies each package to
`<host-skill-library>/<name>/SKILL.md`. **`<host-skill-library>`** is the
skill directory this bot already uses. Resolve it from the agent. Do not
hard-code a machine home directory.

An empty board (no coding workers) is fine. cs-stack is still useful for
verify and review when a human opens pull requests. The coding front door
can stay quiet.

Your coding front-door bot may offer a skippable Install once after GitHub
is connected. You can skip that offer. This guide does not require a
marketplace package.

## 3. Which skills to load

v0 ships a verification + shipping subset. Load what you need:

| Need                                  | Skill                                               |
| ------------------------------------- | --------------------------------------------------- |
| First install / re-wire               | [setup-cs-stack](../skills/setup-cs-stack/SKILL.md) |
| Standing rigor / careful posture      | [careful-mode](../skills/careful-mode/SKILL.md)     |
| Facts before changing code            | [investigate](../skills/investigate/SKILL.md)       |
| Confirmed defect                      | [fix-bug](../skills/fix-bug/SKILL.md)               |
| Intentional feature / behavior change | [ship-change](../skills/ship-change/SKILL.md)       |
| Explicit verification pass            | [verify](../skills/verify/SKILL.md)                 |
| Careful PR review                     | [review-pr](../skills/review-pr/SKILL.md)           |

Index: [skills/README.md](../skills/README.md). Scope: [PLAYBOOK-V0.md](PLAYBOOK-V0.md).

## 4. Verify / review loop (before merge)

1. Agent implements behind a PR (never force-push to `main`).
2. Run **verify** on the change — attach evidence to the PR.
3. Request **review-pr** / human CODEOWNERS review.
4. A **human** approves and merges. Agents do not self-merge.

Binding principles: [human-gate](../principles/human-gate.md),
[no-self-merge](../principles/no-self-merge.md),
[verify](../principles/verify.md).

## 5. Smoke check

- Host skill library lists the packages you installed (at least `setup-cs-stack`; full v0 also includes `careful-mode`, `investigate`, `fix-bug`, `ship-change`, `verify`, `review-pr`).
- Relative links from a skill into `playbooks/` and `principles/` resolve.
- `setup-cs-stack` re-run reports skips, not destructive rewrites.

## Out of scope here

- Restaging a third-party bot template pack
- Publishing to any agent marketplace
- Vendor of third-party playbook source trees ([no-vendor-upstream](../principles/no-vendor-upstream.md))

## Related

- Bot authors (generic roles + front-door FTUE): [FOR-BOT-AUTHORS.md](FOR-BOT-AUTHORS.md)
- Security reports: [SECURITY.md](../SECURITY.md)
