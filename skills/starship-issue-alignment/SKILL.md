---
name: Starship issue alignment
description: >-
  Use when open-issue backlog needs alignment — collapse duplicates, close
  verified-complete, re-rank; Phase 0 provenance hard gate; four-check absorb;
  two-tier rule. Public Soft-pull twin for cs-stack (scrubbed).
---
# Starship issue alignment

**Public Soft-pull twin.** Scrubbed from a private instruction tree (not for public) `issue-alignment`. Complements [Starship issue tracker](../starship-issue-tracker/SKILL.md) (Phase 5 sync) and [Starship issue workflow](../starship-issue-workflow/SKILL.md) (how issues are worked).

## Purpose

Reduce an open-issue backlog to the issues that are actually open work, without destroying tracking. Collapse, don't just close — **migration precedes closure**.

Operator framing:

> Make sure the issues are properly aligned (older issues where the work might already be completed or superseded) and link/close duplicate work so there is one issue open for the scope of work. Take the best-scoped issue and add anything that needs to be added when collapsing so nothing is lost.

## When to use

- Open-issue count has grown untrustworthy
- Before re-ranking a per-repo tracker
- Scheduled hygiene / reconciliation pass
- After many machine-filed monitor issues accumulate

## Hard rules

- [Human gate every step](../../principles/human-gate.md) — **before any bulk close or absorb execute**; show the proposed survivor set, absorb comments, and close list; Silence ≠ OK
- Phase 0 provenance is a **hard gate** — never hand-close an issue filed by a workflow that will re-file it
- Soft-fail if private ops tracker / central priority board is unbound or attach is **not-yet-bound** — keep per-repo alignment; do not invent a board bind
- Never invent tool output (provenance survey, gh JSON, workflow inspection)
- No secrets, tokens, PATs, cookies, vault IDs, or customer PII in comments or ledger text
- No foreign slash commands or private-box-only hooks as hard requirements

## The prior that will mislead you

A backlog review assumes old issues are often already done. **Measure before believing it.** In the Qubes `[sec-review]` block (worked example) — 31 findings, 7 weeks old — **28 of 31 were still exactly true** when re-checked at the file and line they named. Closing that block needed fixes, not triage.

Run Phase 0 and Phase 2 before forming any view of how much is closeable.

---

## Phase 0 — Provenance first (the gate that matters most)

> HARD GATE: never hand-close an issue filed by a workflow that will re-file it. It returns within a day, minus its history. In the Qubes worked example this rule protected 51 issues — 17% of the repo.

### Survey (preferred tool when present)

If the repo (or a clone that has it) includes `tools/issue-provenance-survey.py`, run it:

```bash
tools/issue-provenance-survey.py --repos <REPO> --report
# or fleet-wide with raw data kept:
tools/issue-provenance-survey.py --all --json phase0.json --report
```

**If the tool is absent:** approximate with `gh` + reading `.github/workflows/*` issue-create / dedup / close paths. Document which workflows you inspected and what you concluded. **Never invent survey output** — if you cannot determine an axis, mark `unknown` and spare those issues (superset attribution: spare rather than wrongly close).

It scores every issue-filing workflow on **two independent axes**:

|                         | `closes_on_recovery` yes                                 | `closes_on_recovery` no                                           |
| ----------------------- | -------------------------------------------------------- | ----------------------------------------------------------------- |
| **`dedups_on_open` yes** | **self-cleaning** — DO NOT hand-close; it clears itself | **re-files, never self-clears** — needs a close-on-recovery path |
| **`dedups_on_open` no**  | closes but may duplicate                                 | **UNBOUNDED ACCUMULATOR** — every run files a new issue          |

### Why two axes and not one

Asking only "can this workflow close its own issues?" does not partition the space. The question a reviewer needs answered is **"if I hand-close this, will it come back?"** — which is about **dedup-on-open**, a different property. One axis cannot express workflows that have no close path _and_ re-file (because they dedup and rewrite the body).

### Traps this step exists to avoid

- **`issues.create` is a substring of `issues.createComment`.** A hand grep can over-count issue-filing workflows; comment-only compliance scans are not filers.
- **Author is not provenance.** A workflow running with a PAT files as the PAT owner. Prefer each workflow's own dedup query over author-based counting.
- **The axes are per-workflow; the behaviour is per-PATH.** A workflow may report `dedups_on_open=true` overall while a non-critical path calls `issues.create` with no lookup. **Read any "self-cleaning" row with a large owned population per-path before trusting it.**

Attribution is deliberately a **superset** — it claims every issue the dedup query _would_ see. A superset can only make you spare an issue you could have closed, never close one that re-files.

**Finally, sanity-check every count against a value known by other means** (table re-add, independent `gh` totals).

---

## Phase 1 — Look for the pass that already happened

**Search the repo for a prior reconciliation/audit issue before doing any analysis.**

```bash
gh issue list --repo "$ORG/$REPO" --state all --limit 100 \
  --search "reconciliation OR alignment OR audit OR hygiene in:title"
```

If a prior alignment / reconciliation issue already enumerates duplicate pairs or findings, **work that issue** (post measured scale, execute its plan) instead of filing a second unworked reconciliation issue.

This applies to findings too: if Phase 0 finds an accumulation already filed, post the measured scale there — do not open a new issue.

---

## Phase 2 — Cluster, then adjudicate with the four-check test

Group by title-prefix family and by topic. For each candidate collapse, run **all four checks**:

| # | Check | Fails when… |
| - | ----- | ----------- |
| 1 | Same **failure domain**? | Production outage vs merge gating (different domains) |
| 2 | Would any **acceptance criterion** of the survivor be satisfied by absorbing? | Absorbed work does not meet survivor ACs |
| 3 | Does the absorbed **content have a home**? | Tables, lists, or tracking detail with nowhere to live on the survivor |
| 4 | Is the premise **re-verified against code/API**, not prose? | Claim only appears in issue text; code/API says otherwise |

> **All four must pass. Any single failure = keep separate and record why.**

### Collapsing a machine-filed family: the subset test

For a duplicate family, fingerprint each body with dates, timestamps and ids stripped. Then:

> **Fingerprint equality is sufficient to collapse. Fingerprint inequality is NOT sufficient to refuse.**

When bodies differ, do not stop at "these are not duplicates" and do not fall back to judgement. Extract the **findings** (records, certificates, CVEs, whatever the issue reports) from every member and test whether the survivor is a **superset**. A count that drifts is not a different subject.

**Before any bulk close, also confirm across the whole family:** zero comments, zero assignees, zero milestones, uniform labels. Any one of those means a human engaged with that specific issue and it is no longer a silent duplicate.

### After a dedup fix, re-run Phase 0

Adding dedup to an unbounded accumulator makes it `dedups_on_open=true, closes_on_recovery=false` — **"re-files, never self-clears"**. Better, not resolved:

- The survivor now absorbs a comment per run **forever** until a human acts.
- **Hand-closing the survivor while the condition persists causes a fresh file.** Say so on the survivor.
- A recovered condition becomes **silent** — the alert stops updating, which is indistinguishable from the monitor breaking.

A collapse is finished only when the close-on-recovery gap is filed (or explicitly accepted as known debt), not when the duplicates are closed.

### Verifying "already done"

Close as complete only when completion is **verified against the repo or the API** — never inferred from a comment claiming it.

- For a finding about a deployed artifact, the source file in the wrong repo is not evidence — check what is actually enforced.
- A fix can be half-applied and read as complete. **Verify at the line, not at the symbol.**
- Adjacent hardening can land while the named defect survives.
- Populations grow while an issue sits — re-measure counts the issue asserts.

---

## Phase 3 — Execute in this order (order is not cosmetic)

**Human gate first:** present the proposed absorb set + close list; get explicit OK ([Human gate every step](../../principles/human-gate.md)).

1. **Write the survivor's absorb comment first.** Migration precedes closure, so a failure mid-way leaves content duplicated rather than lost.
2. Then the closing comment: survivor + evidence + ledger reference.
3. Close with the correct reason — `completed` for verified-done work, `not planned` for duplicates/superseded.
4. **Always `--body-file`.** An inline double-quoted body silently deletes every backtick span.

```bash
gh issue comment <survivor> --body-file absorb.md
gh issue comment <duplicate> --body-file close.md
gh issue close <duplicate> --reason "not planned"
```

---

## Phase 4 — Verify this pass

- Both-sides check: every closure names a survivor; every survivor names its sources.
- Reconcile counts by **re-adding the table**, not by trusting the headline.
- Append results to the review ledger; keep one ledger as the single record.

---

## Phase 5 — Sync

- Refresh the per-repo [Starship issue tracker](../starship-issue-tracker/SKILL.md) when one exists (lightweight; human OK if material body change).
- Central / private ops tracker **only if cross-repo ordering changed**, when bound (soft-fail if unbound — do not invent a bind).
- Soft-fail if the board is unbound or not-yet-bound — do not invent a bind; finish per-repo sync only.

---

## Do-not-close list (standing exceptions)

| Category | Example | Why |
| -------- | ------- | --- |
| Self-closing monitor issues | BreakFix / monitor-managed families | re-files in a day, history destroyed |
| **Policy-required audit anchors** | `need rotation:` (or similar) issues | Standing project policy / repo docs may require the tracking issue as the drill's audit anchor — the auto-filed twin is _not_ a duplicate of it |
| Different measured population | credential lists covering different sets | folding one silently drops rotation coverage |
| Unverified absorb claim | an issue asserting it absorbs another | verify the four checks; the claim can be false |
| Auto-issue on `main` | a CI-failure issue on the default branch | needs its failure verified, not swept |

---

## Two-tier rule for repos where you have no context

Deep context will not exist in every repo; the false-positive rate on "these are duplicates" rises accordingly.

- **Tier 1 — safe without context, CLOSE these:** verified-complete with the artifact checked, stale auto-issues on deleted branches, exact-duplicate titles (and Phase 0 allows it).
- **Tier 2 — needs context, LINK AND PROPOSE, do not close:** anything resting on a judgement that two differently-worded issues describe one scope. Leave the decision to a session with context, or to the operator.

This trades a lower closure count for not destroying tracking. The fleet base rate of plausible-but-wrong consolidation is demonstrably non-zero (see worked example).

---

## High-yield checks that are cheap

**Resolve every `blocked` label's blocker.** A stale `blocked` label is invisible indefinitely. A `blocked` label with no ref is an unfalsifiable claim — flag it.

**Check whether a merged PR already did the work.** Flag open issues named by a merged PR — but **flag for human confirmation; never auto-close on a title match**. One PR can fix its issue _and_ spawn three new ones.

---

## Worked example — private ops tracker / sample repo (historical)

Historical note — label as **worked example**, not a live bind requirement.

| | |
| - | - |
| Pass 1 (duplicate clusters) | 16 closed, 319 → 303 |
| Protected by the Phase 0 gate | **51** monitor-managed issues |
| False absorb claim caught | **1** (#2154's claim on #2123) |
| Pass 2 block 1 (`[sec-review]` × 31) | 3 closed — **28 were still live** |
| Pass 2 block 2 (`blocked` × 28) | 1 closed, 6 flagged as unverifiable |
| Org-wide Phase 0 | 47 workflows, 25 repos, 1,100 open → 278 machine / 822 human |

The single most important carry-forward: **run Phase 0 first, in every repo.**

Additional worked-example notes from that pass (keep for judgement calibration):

- Four-check: same header can pass for some absorb targets and fail for others — opposite answers under one claim.
- Machine-filed family subset test: fingerprint-identical DNS-change duplicates collapsed directly; certificate-review bodies differed until CN/SAN findings were extracted and shown to be a superset of the survivor.
- cloudflare-management accumulation already filed as #497 — correct action was to post measured scale, not open a new issue.

---

## Anti-patterns

- Hand-closing monitor / self-cleaning issues without Phase 0
- Bulk close or absorb without [Human gate every step](../../principles/human-gate.md)
- Inventing `issue-provenance-survey.py` output when the tool is missing
- Hard-requiring foreign slash commands (foreign issue-tracker / github-issue slash cmds) — use workflow links instead
- Editing the central deployment board when unbound / not-yet-bound
- Closing on title match to a merged PR without human confirmation

## Related

- [Starship issue tracker](../starship-issue-tracker/SKILL.md) — per-repo tracker this pass syncs in Phase 5
- [Starship issue workflow](../starship-issue-workflow/SKILL.md) — how issues are worked end-to-end
- [Starship github issue](../starship-github-issue/SKILL.md) — issue creation conventions (may install next; soft-fail if missing)
- [Human gate every step](../../principles/human-gate.md)

## Success proof

Phase 0 run (tool or documented gh+workflow approximation); four-check recorded for every absorb; human OK before bulk close/absorb; both-sides ledger consistent; tracker refreshed when present; board soft-failed honestly if unbound.
