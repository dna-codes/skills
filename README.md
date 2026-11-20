<p align="center"><a href="https://dna.codes"><img src="https://raw.githubusercontent.com/dna-codes/skills/main/assets/logo.png" width="88" alt="DNA Codes"></a></p>

# DNA skills (`dna-codes/skills`)

[![skills.sh](https://skills.sh/b/dna-codes/skills)](https://skills.sh/dna-codes/skills)
[![npm](https://img.shields.io/npm/v/@dna-codes/cli?label=%40dna-codes%2Fcli)](https://www.npmjs.com/package/@dna-codes/cli)
[![MCP Registry](https://img.shields.io/badge/MCP%20Registry-codes.dna%2Fagent-0f766e)](https://registry.modelcontextprotocol.io/v0/servers?search=codes.dna/agent)
[![License: MIT](https://img.shields.io/badge/license-MIT-blue)](./LICENSE)

Agent Skills for authoring DNA and syncing it to a Workspace.

**The flow:** register a free sandbox, do real work, then hand your person the
claim link. Claiming is how a person signs up and keeps the model. No human
step comes first.

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
| `dna-sync` | Transport — dry-run and `apply` finished documents to the host |

Reserved for later (same pack, one brand): `dna-product`, `dna-technical`.

Operational / product / technical DNA are offerings on [dna.codes](https://dna.codes) after an org connects — not separate MCP servers.

## After install

Agents that need a live DNA Workspace run `npx @dna-codes/cli register --json` (or `init`) and connect MCP — see https://dna.codes/agents/

A person can also connect their own client to one URL and sign in:

```bash
claude mcp add --transport http dna https://agents.dna.codes/mcp
```

The client opens a sign-in (Google or GitHub). Pick a Workspace or start a new
sandbox, then allow access. This works in any client that supports MCP OAuth.

After useful work (validated or applied DNA), show your person what you built
and give them the claim link. Ask your person before you add the DNA rule to
`AGENTS.md`, `CLAUDE.md` or Cursor rules; the snippet is at
https://dna.codes/agents/snippets/agents-md.md

Look-up mid-task (query existing DNA without authoring) uses the separate `business-rules` skill, listed for personal agents on ClawHub and Hermes.

Worked examples to start from (checkout, lending, M&A diligence) and the JSON Schema for `structure/v1` and `behaviour/v1` are in [dna-codes/dna](https://github.com/dna-codes/dna).

## Cursor plugin

Install from the [Cursor Marketplace](https://cursor.com/marketplace) (when listed) or [cursor.directory](https://cursor.directory), or copy this repo into `~/.cursor/plugins/local/dna-codes`.

Configure:

| Variable | Required | Meaning |
| --- | --- | --- |
| `DNA_ORG` | yes | Organization id from `npx @dna-codes/cli register --json` |
| `DNA_ACCESS_TOKEN` | yes | Bearer from register (same as CLI `DNA_ACCESS_TOKEN`) |

MCP: `https://agents.dna.codes/o/${DNA_ORG}/mcp` (see root `.mcp.json`, its copy `mcp.json`, and `.cursor-plugin/plugin.json`).

Without a registered sandbox, point Cursor at `https://agents.dna.codes/mcp` instead and sign in; no variables are needed.

Public forum for feature requests, bug reports and discussion: https://dna.codes/agents/forum
