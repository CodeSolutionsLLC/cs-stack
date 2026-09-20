---
name: setup-cs-stack
description: >-
  Use on first install of cs-stack or when wiring its playbooks and skills
  into a Grok Bot (or other coding agent) skill library. Idempotent first-run configure.
---

# setup-cs-stack

Idempotent first-run configure for this repository. Prefer Grok / Grok Bot paths.
Optional model and role config must not hard-fail if absent.

## When to use

- Fresh clone of `CodeSolutionsLLC/cs-stack` and you need skills available to the agent
- Re-wiring playbooks and principles into an existing Grok Bot skill library
- Confirming install after pulling updates (safe to re-run)

## Paths this skill may write

Only these destinations. Do not invent other paths.

| Path | Action |
|------|--------|
| `<host-skill-library>/<skill-name>/SKILL.md` | Copy or update each package under `skills/*/SKILL.md` from this checkout into the host skill library the agent already uses |
| Optional agent models config (host-defined) | Apply Grok-first defaults **only if** a documented models file already exists; otherwise skip |
| Optional agent roles config (host-defined) | Apply light role wiring **only if** a documented roles file already exists; otherwise skip |

`<host-skill-library>` is the skill directory the running agent already uses (for example a Grok Bot skill library root, or another coding agent's skill directory). Resolve it from the agent environment; do not hard-code machine-specific or private host paths in this repo.

This skill does **not** write secrets, PATs, or credentials anywhere.

## Steps (idempotent)

1. **Locate checkout.** Confirm this tree is a `cs-stack` clone (presence of `playbooks/`, `principles/`, `skills/`, `NOTICE`). If missing, stop and ask the operator to clone first.
2. **Install skill packages.** For each `skills/<name>/SKILL.md` in this checkout (including this file):
   - Destination: `<host-skill-library>/<name>/SKILL.md`
   - If destination exists and content matches source, skip
   - If missing or different, copy/update the file only (no wipe of sibling skills)
3. **Optional models / roles (fail-open).** Prefer Grok / Grok Bot defaults when applying config. If the host has no models or roles file, continue and note the skip. Never create secret-bearing files.
4. **Point at principles and playbooks.** Tell the agent to read (do not duplicate wholesale):
   - [principles/](../../principles/README.md)
   - [playbooks/](../../playbooks/README.md)
   - Scope: [docs/PLAYBOOK-V0.md](../../docs/PLAYBOOK-V0.md)
5. **Verify.**
   - List `<host-skill-library>/*/SKILL.md` and confirm these v0 packages are present when installed from this checkout: `setup-cs-stack`, `careful-mode`, `investigate`, `fix-bug`, `ship-change`, `verify`, `review-pr`
   - Confirm the relative links above resolve from this file
   - Re-run this skill: second pass must report skips, not destructive changes

## Do not

- Vendor third-party playbook or plugin source files ([no-vendor-upstream](../../principles/no-vendor-upstream.md))
- Disable CI, self-merge, or bypass human gates ([no-self-merge](../../principles/no-self-merge.md), [human-gate](../../principles/human-gate.md))
- Expand into third-party marketplace packaging (out of scope)
- Paste PATs or secrets into chat or into files this skill writes
- Invent private internal skill names or paths in public files
