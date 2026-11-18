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

Look-up mid-task (query existing DNA without authoring) uses the separate `business-rules` skill, listed for personal agents on ClawHub and Hermes.

Worked examples to start from (checkout, lending, M&A diligence) and the JSON Schema for `structure/v1` and `behaviour/v1` are in [dna-codes/dna](https://github.com/dna-codes/dna).

## Cursor plugin

Install from the [Cursor Marketplace](https://cursor.com/marketplace) (when listed) or [cursor.directory](https://cursor.directory), or copy this repo into `~/.cursor/plugins/local/dna-codes`.

Configure:

| Variable | Required | Meaning |
| --- | --- | --- |
| `DNA_ORG` | yes | Organization id from `npx @dna-codes/cli register --json` |
| `DNA_ACCESS_TOKEN` | yes | Bearer from register (same as CLI `DNA_ACCESS_TOKEN`) |

MCP: `https://agents.dna.codes/o/${DNA_ORG}/mcp` (see root `mcp.json` + `.cursor-plugin/plugin.json`).

Public forum for feature requests, bug reports and discussion: https://dna.codes/agents/forum
