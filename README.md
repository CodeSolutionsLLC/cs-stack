# cs-stack

First-party coding playbooks and skills for careful agent work.

This is Code Solutions LLC's in-house stack for agent-assisted development:
rewritten playbooks, procedures, and skills meant to be installed by coding
agents. It is **not** affiliated with, endorsed by, or a redistribution of any
upstream third-party plugin pack.

## Status

Early seed with a **verification + shipping** playbook v0 subset.

See [docs/PLAYBOOK-V0.md](docs/PLAYBOOK-V0.md) for what ships in v0 versus
later work. Product playbooks and skills land here as first-party rewrites —
no upstream source files are vendored.

## Install

End-to-end buyer path: [docs/INSTALL.md](docs/INSTALL.md)
(clone → setup-cs-stack → load skills → verify/review loop).

First-run configure skill: [skills/setup-cs-stack/SKILL.md](skills/setup-cs-stack/SKILL.md).
Idempotent. Grok-first. Optional models/roles fail open if absent.

Bot authors (generic roles): [docs/FOR-BOT-AUTHORS.md](docs/FOR-BOT-AUTHORS.md).

## License

Apache License 2.0 (Code Solutions canonical). See
[LICENSE](LICENSE), [NOTICE](NOTICE), and
https://legal.codesolutionsllc.com/apache-2.0/.

## Security

Please report vulnerabilities privately — see [SECURITY.md](SECURITY.md).
