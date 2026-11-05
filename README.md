# DNA skills (`dna-codes/skills`)

[![skills.sh](https://skills.sh/b/dna-codes/skills)](https://skills.sh/dna-codes/skills)

Agent Skills for authoring DNA and syncing it to a Workspace.

Install:

```bash
npx skills add dna-codes/skills
```

List without installing:

```bash
npx skills add dna-codes/skills --list
```

## Skills

| Skill | Role |
| --- | --- |
| `dna-operational` | Craft — structure (`structure/v1`) + behaviour (`behaviour/v1`) for **operational** DNA |
| `dna-sync` | Transport — `validate` / `apply` finished documents to the host |

Reserved for later (same pack, one brand): `dna-product`, `dna-technical`.

Operational / product / technical DNA are offerings on [dna.codes](https://dna.codes) after an org connects — not separate MCP servers.

## After install

Agents that need a live DNA Workspace still run `npx @dna-codes/cli register` (or `init`) and connect MCP — see https://dna.codes/agents/

Look-up mid-task (query existing DNA without authoring) uses the separate `business-rules` skill packaged with ClawHub/Hermes listings in the platform repo.

## Source of truth

This repository **is** the canonical skill tree. The private platform checks it out as a git submodule (no second copy).
