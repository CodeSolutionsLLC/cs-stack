# Playbooks

First-party agent playbooks for cs-stack. Short, actionable procedures —
rewritten in-house, not copied from upstream packs.

## v0 (verification + shipping)

| Playbook | Purpose |
|----------|---------|
| [investigate.md](investigate.md) | Gather facts before changing behavior |
| [fix-bug.md](fix-bug.md) | Reproduce, fix, and verify a defect |
| [ship-change.md](ship-change.md) | Land a feature or intentional change |
| [verify.md](verify.md) | Run an explicit verification pass |
| [review-pr.md](review-pr.md) | Review a pull request carefully |

See [docs/PLAYBOOK-V0.md](../docs/PLAYBOOK-V0.md) for what is in / out of v0.

## Conventions

- Keep stubs short; expand only with first-party prose.
- Prefer checklists over long narrative.
- Never vendor upstream playbook files into this tree.
