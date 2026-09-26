# For Grok Bot authors

Short notes for people who design or configure Grok Bots that consume
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

## Front-door FTUE (public templates)

Public front-door / dispatcher templates must treat **cs-stack Install as
skippable**. After GitHub (or equivalent) is connected, offer Install once;
the operator may skip. Soft-skip is not the same as “install OK.”

**Do not** block the front door’s “ready” state on:

- a failed `git clone` of this repo
- missing coding workers on an empty board
- incomplete skill-library wiring

An empty board still works: a human can open PRs; workers (when present) use
the verify → review loop. The front door stays quiet until there is a leaf to
assign.

v0 scope for authors: **verification + shipping only** — see
[PLAYBOOK-V0.md](PLAYBOOK-V0.md). Do not teach templates to require the full
future playbook catalog before they can declare ready.

Buyer path details: [INSTALL.md](INSTALL.md).

**FOLLOW-UP (#22):** when `docs/INSTALL.md` gains the **Grok Bot importer**
subsection (Windows / skill-library destination / Skip is OK), link that
heading from this section. Until then, INSTALL remains the canonical install
path; do not hard-require it for ready.

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
- Block “ready” on failed cs-stack clone or missing workers (see Front-door
  FTUE above)

Full install path: [INSTALL.md](INSTALL.md).
