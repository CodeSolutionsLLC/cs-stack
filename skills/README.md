# Skills

First-party installable skills for cs-stack v0. Each package is a directory
with `SKILL.md` (YAML frontmatter: `name`, `description`). Skills operationalize
playbooks — they do not vendor upstream packs.

## Install

Clone this repository and point the agent at `skills/<name>/`. Copy or symlink
a package into the agent’s skill path if the host requires a local library.
Load only the packages you need; sibling leaves may land packages at different
times.

## v0 index

| Skill | Package | Playbook | Leaf |
|-------|---------|----------|------|
| verify | [verify/SKILL.md](verify/SKILL.md) | [playbooks/verify.md](../playbooks/verify.md) | this (#6) |
| review-pr | [review-pr/SKILL.md](review-pr/SKILL.md) | [playbooks/review-pr.md](../playbooks/review-pr.md) | this (#6) |
| investigate | sibling — not authored here | [playbooks/investigate.md](../playbooks/investigate.md) | [#5](https://github.com/CodeSolutionsLLC/cs-stack/issues/5) |
| fix-bug | sibling — not authored here | [playbooks/fix-bug.md](../playbooks/fix-bug.md) | [#5](https://github.com/CodeSolutionsLLC/cs-stack/issues/5) |
| ship-change | sibling — not authored here | [playbooks/ship-change.md](../playbooks/ship-change.md) | [#5](https://github.com/CodeSolutionsLLC/cs-stack/issues/5) |
| setup-cs-stack | sibling — not authored here | first-run configure (no dedicated v0 playbook) | [#4](https://github.com/CodeSolutionsLLC/cs-stack/issues/4) |

This leaf authors **verify** and **review-pr** only. Sibling packages are
indexed for install planning; their `SKILL.md` files land on those leaves.

Prove-it and no-self-merge are binding: [principles/verify.md](../principles/verify.md),
[principles/no-self-merge.md](../principles/no-self-merge.md),
[principles/human-gate.md](../principles/human-gate.md).

See [docs/PLAYBOOK-V0.md](../docs/PLAYBOOK-V0.md).
