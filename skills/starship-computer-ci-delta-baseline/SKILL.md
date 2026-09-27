---
name: Starship computer CI delta baseline
description: >-
  Use when the coordinator scopes CI for a leaf — one-time heavy baseline, then only
  delta + touched systems; local first, remote sparingly; new full baseline when
  CI config changes; drills catch gate loops; scan/bundle related issues when CI
  must change. Public Soft-pull twin for cs-stack (scrubbed).
---
# Starship computer CI delta baseline

**Public Soft-pull twin.** Keep remote CI cost low without skipping merge/ship gates. Untouched systems are not re-tested every leaf — **drills** prove the gate loops still catch misses before launch.

## Model

1. **Baseline (rare, heavy)** — full green pass that establishes trust for the current CI config + critical systems. Record `ci-baseline:{repo}:{hash-or-date}` (config fingerprint + when it passed).
2. **Delta + touched (default)** — for each leaf, run only tests for:
   - the **delta** (changed paths), and
   - any **systems touched** by that delta (deps, callers, shared contracts)
   - If a system was **not** touched → do **not** burn CI on it this leaf
3. **Simulate** the change → **human gate** ([Human gate every step](../../principles/human-gate.md)) → then remote only if still needed
4. **Local always first** — Local CI env install; prove delta locally before paying for remote workers
5. **Remote sparingly** — workers/CloudAgent remote CI only after local green (or local impossible with recorded reason). No always-on misconfigured remote burn

## When CI config itself changes

If workflows / required checks / CI installer differ from the **last baseline fingerprint**:

1. **Do not** treat prior baseline as valid
2. The coordinator **scans** related open issues / leaves that share that CI surface
3. **Bundle** compatible CI + related fixes into **fewer PRs** where safe (one change-at-a-time still applies inside the PR; fewer baselines overall)
4. Run a **new full baseline** to confirm green
5. Record the new fingerprint

CI changes should be **rare but ongoing** (stay patched; only the paranoid survive). Cost of a new baseline is accepted when CI must move.

## Drills (catch the loops)

Periodic or pre-launch **drills** (not sub-hourly polls):

- Exercise the full gate chain on a known path: delta select → local → sim → human gate → (optional) remote
- Intentionally include a case where an “untouched” system would have been wrongly skipped if mapping were wrong — confirm drill **fails closed** or mapping is corrected
- Record `ci-drill:{repo}:{iso}: PASS|FAIL` — FAIL blocks launch until fixed
- Purpose: nothing missed before light; keep day-to-day CI on delta only

## Leaf CI scope card (show at human gate)

```text
CI scope — {leaf}
baseline: {id} (still valid? yes/no)
delta paths: …
systems touched: …
local: PASS|FAIL|SKIP(reason)
remote: not-yet | PASS | FAIL | skipped(local-only OK)
drill since baseline: …
```

## Anti-patterns

- Full-suite remote on every tiny leaf while baseline is valid
- Skipping local and jumping to remote “because it’s easier”
- Keeping remote CI spinning on bad config
- Changing CI and shipping on the **old** baseline
- Many tiny CI-config PRs that each force a new baseline when one bundled PR would do
- Using delta scope to skip tests for a system the delta **did** touch

## Fits with

- [Starship computer gates](../starship-computer-gates/SKILL.md)
- Local CI env install

## Success proof

Leaf shows valid baseline + delta/touched scope + local result + sim/human OK; CI config changes produced a new baseline (after related-issue scan/bundle when possible); drills recorded since last baseline or explicitly scheduled.
