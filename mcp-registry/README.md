# DNA MCP registry assets

Public icons and notes for the official MCP Registry listing
[`codes.dna/agent`](https://registry.modelcontextprotocol.io/?q=codes.dna).

The MCP endpoint is remote-only:

```
https://agents.dna.codes/o/{org}/mcp
```

Register or init first (`npx @dna-codes/cli register`), then connect with a
bearer token. Craft/sync skills for the same product live in this repository
(`dna-operational`, `dna-sync`).

## Shipped authoring tools (always on `tools/list`)

`describe`, `validate`, `apply`, `get`, `list`, `diff`, `set-egress-allowlist`

- Full contracts: https://dna.codes/agents/docs/tools.md
- `set-egress-allowlist` is **person-only** on a claimed organization
- After behaviour DNA is applied, `tools/list` also includes Action-derived
  business tools named by that DNA — not a fixed catalog in this pack

Do not use brief aliases (`dna_search`, `dna_describe`, `dna_start`).

The platform monorepo that hosts the cell is private; this folder is the public
repository reference for the listing.
