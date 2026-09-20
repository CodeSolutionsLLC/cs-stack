# Contributing

Thanks for helping improve **cs-stack**. Contributions are under the
[Apache License 2.0](LICENSE) (Code Solutions canonical). See [NOTICE](NOTICE).

## Before you start

1. Read the buyer install path: [docs/INSTALL.md](docs/INSTALL.md).
2. Know what is in v0: [docs/PLAYBOOK-V0.md](docs/PLAYBOOK-V0.md).
3. Report security issues privately — [SECURITY.md](SECURITY.md). Do **not**
   open a public issue or PR with exploit details or secrets.

## How we land changes

1. Open a PR against `main` with a clear summary and how to verify.
2. Keep CI green (validate / required checks on the PR).
3. Wait for **CODEOWNERS** review ([.github/CODEOWNERS](.github/CODEOWNERS)).
4. A **human** approves and merges. Authors do not self-merge.

Principles: [human-gate](principles/human-gate.md),
[no-self-merge](principles/no-self-merge.md),
[verify](principles/verify.md).

## Do

- Prefer small, reviewable PRs with evidence (commands, test output, links).
- Follow first-party playbooks and skills under `playbooks/` and `skills/`.
- Keep public docs free of private credentials and internal roster names.

## Do not

- Put secrets, PATs, or private config in the tree or in PR bodies.
- Vendor third-party playbook or skill **source packs** into this repo
  ([no-vendor-upstream](principles/no-vendor-upstream.md)). Ideas-only
  attribution stays in NOTICE — do not copy upstream files.
- Bypass branch protection, required checks, or CODEOWNERS “to save time.”
- Force-push to `main`.

## Questions

Use GitHub issues for product discussion. Security: [SECURITY.md](SECURITY.md).
