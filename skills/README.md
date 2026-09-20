# Skills

First-party installable skills for cs-stack v0. Each package is a directory
with `SKILL.md` (YAML frontmatter: `name`, `description`). Skills operationalize
playbooks — they do not vendor upstream packs.

## Install

Use [`setup-cs-stack`](setup-cs-stack/SKILL.md) for first-run configure, or clone
this repository and copy/symlink `skills/<name>/` into the agent’s skill library.
Load only the packages you need.

## v0 index

| Skill | Package | Playbook |
|-------|---------|----------|
| setup-cs-stack | [setup-cs-stack/SKILL.md](setup-cs-stack/SKILL.md) | first-run configure |
| investigate | [investigate/SKILL.md](investigate/SKILL.md) | [playbooks/investigate.md](../playbooks/investigate.md) |
| fix-bug | [fix-bug/SKILL.md](fix-bug/SKILL.md) | [playbooks/fix-bug.md](../playbooks/fix-bug.md) |
| ship-change | [ship-change/SKILL.md](ship-change/SKILL.md) | [playbooks/ship-change.md](../playbooks/ship-change.md) |
| verify | [verify/SKILL.md](verify/SKILL.md) | [playbooks/verify.md](../playbooks/verify.md) |
| review-pr | [review-pr/SKILL.md](review-pr/SKILL.md) | [playbooks/review-pr.md](../playbooks/review-pr.md) |

Prove-it and no-self-merge are binding: [principles/verify.md](../principles/verify.md),
[principles/no-self-merge.md](../principles/no-self-merge.md),
[principles/human-gate.md](../principles/human-gate.md).

See [docs/PLAYBOOK-V0.md](../docs/PLAYBOOK-V0.md).
