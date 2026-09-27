---
name: setup-cs-stack
description: >-
  Use on first install of cs-stack or when wiring its playbooks and skills
  into a Grok Bot skill library. Idempotent first-run configure. Choose one
  install profile per run: full, leaf, or careful-only.
---

# setup-cs-stack

Idempotent first-run configure for this repository. Install and paths are for Grok Bot only.
Optional model and role config must not hard-fail if absent.

Each run installs one profile. Copy only the packages in that profile from `skills/*/`.

## When to use

- Fresh clone of `CodeSolutionsLLC/cs-stack` and you need skills available to the agent
- Re-wiring playbooks and principles into an existing Grok Bot skill library
- Confirming install after pulling updates (safe to re-run)
- Switching profile on a later run (copy the new set; leave packages already present)

## Paths this skill may write

Only these destinations. Do not invent other paths.

| Path | Action |
|------|--------|
| `<host-skill-library>/<skill-name>/SKILL.md` | Copy or update each package in the resolved profile from `skills/<name>/SKILL.md` in this checkout into the host skill library the agent already uses |
| Optional agent models config (host-defined) | Apply Grok Bot defaults **only if** a documented models file already exists; otherwise skip |
| Optional agent roles config (host-defined) | Apply light role wiring **only if** a documented roles file already exists; otherwise skip |

`<host-skill-library>` is the skill directory the running Grok Bot already uses (for example a Grok Bot skill library root). Resolve it from the agent environment; do not hard-code machine-specific or private host paths in this repo.

This skill does **not** write secrets, PATs, or credentials anywhere. It does not write the chosen profile to a new file.

## Install profiles

Choose exactly one profile per run. Include an optional package only when the operator names it in that same choice.

| Profile | Always install | Optional |
|---------|----------------|----------|
| `full` | `setup-cs-stack`, `careful-mode`, `investigate`, `fix-bug`, `ship-change`, `verify`, `review-pr` | — |
| `leaf` | `setup-cs-stack`, `investigate`, `fix-bug`, `verify` | `careful-mode` |
| `careful-only` | `setup-cs-stack`, `careful-mode` | `verify` |

`leaf` is `investigate`, `fix-bug`, and `verify`, plus `careful-mode` when the operator asks for it. `careful-only` is `careful-mode`, plus `verify` when the operator asks for it. Every profile also copies `setup-cs-stack` so the operator can re-run profile selection from the host skill library.

## Steps (idempotent)

1. **Locate checkout.** Confirm this tree is a `cs-stack` clone (presence of `playbooks/`, `principles/`, `skills/`, `NOTICE`). If missing, stop and ask the operator to clone first.
2. **Choose profile (once per run).** Resolve the install set before copying anything.
   - If the operator already passed a profile argument, use it: `full`, `leaf`, or `careful-only`.
   - If no profile was given, ask once which profile to install and wait. Do not copy files until they name one of those three. Do not assume `full`.
   - If the answer is not one of those three names, stop and ask again. Leave the library unchanged.
   - Optionals, same choice:
     - `leaf`: include `careful-mode` only when the operator names it. Otherwise omit it.
     - `careful-only`: include `verify` only when the operator names it. Otherwise omit it.
     - `full` has no optional packages. Ignore extra package names.
   - Examples the operator can pass: `full`; `leaf`; `leaf` with `careful-mode`; `careful-only`; `careful-only` with `verify`.
   - A later run takes a new argument or answers the question again. Same profile and same optionals must be safe to repeat.
3. **Install skill packages.** For each name in the resolved install set:
   - Source: `skills/<name>/SKILL.md` in this checkout
   - Destination: `<host-skill-library>/<name>/SKILL.md`
   - If destination exists and content matches source, skip
   - If missing or different, copy/update that file only (no wipe of sibling skills)
   - Skip every `skills/<name>/` directory that is not in the resolved set
4. **Optional models / roles (fail-open).** Apply Grok Bot defaults when applying config. If the host has no models or roles file, continue and note the skip. Never create secret-bearing files.
5. **Point at principles and playbooks.** Tell the agent to read (do not duplicate wholesale):
   - [principles/](../../principles/README.md)
   - [playbooks/](../../playbooks/README.md)
   - Scope: [docs/PLAYBOOK-V0.md](../../docs/PLAYBOOK-V0.md)
6. **Verify.** Check the resolved install set for this run only.
   - For each name in that set, confirm `<host-skill-library>/<name>/SKILL.md` is present
   - `full` confirms `setup-cs-stack`, `careful-mode`, `investigate`, `fix-bug`, `ship-change`, `verify`, `review-pr`
   - `leaf` confirms `setup-cs-stack`, `investigate`, `fix-bug`, `verify`, and `careful-mode` only when it was included
   - `careful-only` confirms `setup-cs-stack`, `careful-mode`, and `verify` only when it was included
   - Do not require packages outside that set
   - Confirm the relative links above resolve from this file
   - Re-run this skill with the same profile and the same optional choices: the second pass must report skips, not destructive changes

## Do not

- Vendor third-party playbook or plugin source files ([no-vendor-upstream](../../principles/no-vendor-upstream.md))
- Disable CI, self-merge, or bypass human gates ([no-self-merge](../../principles/no-self-merge.md), [human-gate](../../principles/human-gate.md))
- Paste PATs or secrets into chat or into files this skill writes
- Invent private internal skill names or paths in public files
- Copy `skills/*/` packages that are outside the resolved profile
