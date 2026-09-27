---
name: Starship computer humans onboard
description: >-
  Use on coordinator first start and when people check in/out — record how many
  people use the workspace, confirm each label, and on count 0 or owner reset
  require confirm before clearing memory/connectors (no silent wipe).
  Public Soft-pull twin for cs-stack (scrubbed). Use “people on this workspace” copy only;
  never SpaceX/Starship affiliation language.
---
# Starship computer humans onboard

**Public Soft-pull twin.** User-facing strings below are brand-safe. Public copy must not imply SpaceX/Starship affiliation — use “people on this workspace” language only.

The coordinator stays tied to the people on the workspace (ops check-in/out inspired). If count → 0, the coordinator **resets after confirm** — it does not keep flying empty.

## Onboard memory keys (labels only — no secrets)

- `humans-onboard: N`
- `human:{i}: {name or role label}`
- `humans-onboard-initialized: yes`
- After reset: clear the above toward default; record `last-reset-at: {iso}` (no secret payloads)

## First start (count unset)

1. Send the **exact** first-run copy (below), then collect number + each label.
2. Confirm the list back once; on OK record memory.
3. Do not start board bind / heavy ops until initialized (or maintainer overwrites).

### First-run copy (PASS — use verbatim)

> **People on this workspace**
>
> Before the coordinator starts work, tell us how many people use this workspace with the coordinator.
>
> Send the number, then confirm each person’s name or role label (for example: “1 — Alex (owner)”).
>
> If that count later reaches zero, the coordinator will ask you to confirm a **reset** before clearing anything. A reset clears the coordinator’s memory for this workspace and disconnects account-linked connectors and local scratch data **where the product allows**. It is **not** a cryptographic wipe of all vendor-side logs, and it does **not** remove the coordinator from your sidebar (you can Delete the agent in the app if you want the instance gone).
>
> You can also request a full reset anytime; the coordinator will always confirm first.
>
> This is product security guidance, not legal advice.

## Check-in / check-out

- Owner may add/remove people with **explicit OK** for that change ([Human gate every step](../../principles/human-gate.md)).
- Update `humans-onboard` and labels; one change at a time.

## Count → 0 or owner-requested full reset

**No silent wipe.** Always send the confirm widget. Silence / dismiss = **nothing is cleared**.

### Confirm copy (PASS — use verbatim)

> **Confirm reset**
>
> People on this workspace is **0** (or you asked for a full reset).
>
> If you confirm, the coordinator will:
> - clear its memory for this workspace toward a clean default
> - disconnect account-linked connectors and local scratch data where tools allow
> - stop automation loops and leave boards unbound until someone is added again
>
> This is **not** a full/secure erase of all vendor-side logs, and the coordinator **cannot** delete its own sidebar row (use Delete in the app if you want the instance gone).
>
> This is product security guidance, not legal advice.

Widget: **Reset now** / **Cancel — add someone back**

### On Reset now

1. Stop `auto-*` loops; unbind private ops tracker until re-init.
2. Clear agent memory toward default (workspace secrets/labels for this coordinator).
3. Disconnect MCP / connector bindings and wipe local scratch the coordinator used for this account **where tools allow**.
4. Tell the owner reset finished in one short line (honest scope already on the confirm card). They may Delete the agent in the sidebar if they want the instance gone.
5. **Immediately restart onboarding (required last step):** send a **question widget** (ends the turn):
   - Prompt: **Start people-on-this-workspace setup?**
   - **Yes — start setup** (primary) → next turn send the **exact PASS first-run copy** (“People on this workspace…”) and collect number + labels as in First start.
   - **Not now** → stay clean/reset; remember onboard uninitialized; offer setup later when they ask.
6. Silence/dismiss on the restart widget = treat as **Not now** (do not invent people).

### On Cancel / dismiss

Do not clear. Offer check-in.

## Anti-patterns

- Auto-clearing on 0 without confirm
- Claiming cryptographic / compliance-grade wipe of all vendor logs
- Implying SpaceX/Starship affiliation in public copy
- Self-deleting the sidebar agent (impossible — instruct owner)
- Skipping post-reset onboarding restart widget after a completed Reset now

## Success proof

Onboard count recorded with confirmed labels; any reset used the PASS confirm copy and only ran after **Reset now**; post-reset restart widget fired (Yes → first-run copy, Not now/dismiss → stay clean).
