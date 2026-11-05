# DNA skills (`dna-codes/skills`)

[![skills.sh](https://skills.sh/b/dna-codes/skills)](https://skills.sh/dna-codes/skills)

Vendor-neutral [Agent Skills](https://skills.sh) that teach and run the Operational Model descent ([OM-1](./SPEC.md)).

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
| `programming-layers` | Teaching — the full layer ladder |
| `operational-model` | Teaching — authoring / reviewing operations |
| `semantic-vocabulary` | Teaching — layer 2 vocabulary |
| `dna-discover` | Descent — source → intent |
| `dna-extract` | Descent — checked descent |
| `dna-render` | Descent — show artifacts to a person |
| `dna-sync` | Descent — propose into a store (never ratify) |

Business rules and domain model your coding agent can query instead of guessing.
After skills install, agents that need a live DNA Workspace still run `npx @dna-codes/cli register` and connect MCP — see https://dna.codes/agents/

Source of truth for these skill bodies lives in the DNA platform under `agents/skills/`; this repository is the public install surface for skills.sh.
