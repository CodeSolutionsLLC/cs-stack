# cs-stack

First-party coding playbooks and skills for careful agent work.

This is Code Solutions LLC's in-house stack for agent-assisted development:
rewritten playbooks, procedures, and skills meant to be installed into a
Grok Bot skill library. It is **not** affiliated with, endorsed by, or a
redistribution of any upstream third-party plugin pack.

## Status

**v0 verification + shipping skills are shipped.** Packages on `main`:
`setup-cs-stack`, `investigate`, `fix-bug`, `ship-change`, `verify`,
`review-pr`, `careful-mode`. Index: [skills/README.md](skills/README.md).

Broader playbooks later. See
[docs/PLAYBOOK-V0.md](docs/PLAYBOOK-V0.md) for v0 versus later work.
Product playbooks and skills land here as first-party rewrites — no
upstream source files are vendored.

## Install

End-to-end buyer path: [docs/INSTALL.md](docs/INSTALL.md)
(clone → setup-cs-stack → load skills → verify/review loop).

First-run configure skill: [skills/setup-cs-stack/SKILL.md](skills/setup-cs-stack/SKILL.md).
Idempotent. Grok Bot only. Optional models/roles fail open if absent.

Grok Bot authors (generic roles): [docs/FOR-BOT-AUTHORS.md](docs/FOR-BOT-AUTHORS.md).

Contributing: [CONTRIBUTING.md](CONTRIBUTING.md).

## License

Apache License 2.0 (Code Solutions canonical). See
[LICENSE](LICENSE), [NOTICE](NOTICE), and
https://legal.codesolutionsllc.com/apache-2.0/.

## Security

Please report vulnerabilities privately — see [SECURITY.md](SECURITY.md).
