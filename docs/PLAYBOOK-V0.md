# Playbook v0 scope

cs-stack v0 ships a **verification + shipping** subset only. Full coverage of
a broader playbook catalog (often discussed as ~23 procedures) is explicitly
out of scope for this release.

All playbooks here are first-party rewrites. No third-party playbook or
plugin source files are vendored.

## v0 skills (shipped)

Verification + shipping packages on `main`: `setup-cs-stack`,
`investigate`, `fix-bug`, `ship-change`, `verify`, `review-pr`,
`careful-mode`. Index: [skills/README.md](../skills/README.md).

## Scope table

| Area | Decision | Notes |
|------|----------|-------|
| Investigate | **Ship in v0** | Narrow facts before changing code |
| Fix bug | **Ship in v0** | Reproduce → fix → verify |
| Ship change | **Ship in v0** | Feature/shipping path with human gate |
| Verify | **Ship in v0** | Explicit verification checklist |
| Review PR | **Ship in v0** | Reviewer checklist; no self-merge |
| Broader planning / design playbooks | **Later** | After v0 proves the core loop |
| Domain-specific ops playbooks | **Later** | Product- and infra-specific |
| Full ~23 playbook catalog | **Later / drop many** | Prefer lean first-party set over parity |
| Vendor upstream playbook files | **Drop** | Ideas-only attribution in NOTICE |

## v0 principles (seed)

See [principles/](../principles/README.md): careful, verify, no-self-merge,
human-gate, no-vendor-upstream.

## Index

Playbook index: [playbooks/README.md](../playbooks/README.md).
