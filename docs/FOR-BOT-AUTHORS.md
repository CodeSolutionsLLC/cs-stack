# For bot authors

Short notes for people who design or configure coding agents that consume
cs-stack. Uses **generic roles only** — not any one organization’s internal
roster names.

## Roles that fit this stack

| Role (generic)          | Typical use of cs-stack                                                |
| ----------------------- | ---------------------------------------------------------------------- |
| Coding worker           | Runs investigate / fix-bug / ship-change; opens PRs; never self-merges |
| Reviewer                | Runs review-pr; independent approval voice                             |
| Front door / dispatcher | Assigns one leaf at a time; does not implement the leaf                |
| Secrets broker          | Out of band — cs-stack skills must not hold PATs or vault material     |

Keep **producer → reviewer → human merge** separate. The same identity must not
be the sole approver of its own PR ([no-self-merge](../principles/no-self-merge.md)).

## Wiring

1. Point the agent’s skill library at packages under `skills/` (via
   [setup-cs-stack](../skills/setup-cs-stack/SKILL.md) or an equivalent copy).
2. Prefer reading [principles/](../principles/README.md) and
   [playbooks/](../playbooks/README.md) over pasting them into the bot profile.
3. Encode anti-jobs in the bot description: no self-merge, no CI bypass, no
   secret paste into chat.

## Do not

- Name private fleet bots or seats in public docs derived from this guide
- Vendor third-party playbook/skill source into the bot tree
- Teach the agent to bypass branch protection “to save time”

Full install path: [INSTALL.md](INSTALL.md).
