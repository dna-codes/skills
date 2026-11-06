---
name: dna-sync
description: Sync finished structure/v1 and behaviour/v1 documents to a DNA Workspace via the shipped MCP authoring tools (describe, list, get, validate, apply, diff, set-egress-allowlist). Use when asked to save, push, write, or apply DNA to the server after drafting. Not for discovering or inventing the model (use dna-operational), and not for looking up existing rules mid-task without writing (use business-rules).
---
The public forum is https://dna.codes/agents/forum.


# Sync

Get authored operational DNA onto the organization's Workspace. This skill is
**transport**, not craft — draft with [`dna-operational`](../dna-operational/SKILL.md)
(or an existing document) first.

## First: does this need the server?

If someone asked what a model *would* look like, return the documents and stop.
Reach for MCP only when asked to make the model **durable** in a Workspace.

## Connect

Prefer:

```bash
npx @dna-codes/cli register --json
npx @dna-codes/cli mcp
```

MCP URL shape: `https://agents.dna.codes/o/{org}/mcp` with
`Authorization: Bearer <token>`. There is no `dna_start` and no registration MCP
tool. Guides: https://dna.codes/agents/guides/agent-quickstart.md

Canonical tool reference: https://dna.codes/agents/docs/tools.md

## Shipped authoring tools

These seven are always on `tools/list` (profile v1). Names are fixed — do not
invent brief-style aliases.

| Tool | Role |
| --- | --- |
| `describe` | Names the grammar: profile, definition schemas (structure + behaviour), and the authoring tool list. Reads nothing stored. |
| `list` | Summarises stored domains and behaviour documents at their latest versions. |
| `get` | Reads one stored domain or behaviour document (optional version / entity / relationship). |
| `validate` | Checks a document against a definition (and behaviour against stored structure); writes nothing. |
| `apply` | Writes whole domain and/or behaviour documents as one governed act (envelope with intent; `dryRun` decides and writes nothing). |
| `diff` | Compares stored versions, or a stored version with a proposed document. |
| `set-egress-allowlist` | Replaces the claimed organization's egress allowlist. **Person only** — agents are refused. |

Do **not** call `dna_search`, `dna_describe`, or `dna_start`.

### Derived business tools

After behaviour DNA is applied, `tools/list` also exposes **Action ids** from
that behaviour as additional tools (for example `check-out`). Those names come
from DNA, not from this skill. Call `tools/list` or `describe` to see what this
organization currently offers; never invent Action names.

## Procedure

1. `list` (and `describe` if you need a definition schema).
2. `get` any document you are changing — sync lands **in context** of what
   already exists; collisions are most of what review is for.
3. Prepare the `structure/v1` and/or `behaviour/v1` document(s). Ids stay stable.
4. `validate`, then `apply` with the expected base versions.
5. On `version_conflict`, `get` / `diff`, reconcile, retry.
6. On limit refusals (`needs_claim`, `allowance_exhausted`, `plan_limit`,
   `spend_cap_reached`), follow `refusal.next` — hand the person the claim or
   spend path; do not invent a payment API.

Optional: `dryRun` on `apply` when the host accepts it — use it to preview, not
as a substitute for saying what remains undecided.

## Standing of what you write

`apply` versions live domain and behaviour documents under host governance.
That is syncing to the server — it is **not** the agent ratifying spend, claim,
or external acts on its own. When claim or approval is required, stop and give
the person what they need: what changed, what it grounds in, gaps, and collisions
with existing DNA. External effects stay refused until claim; after claim, only
destinations on the egress allowlist (set by a person via `set-egress-allowlist`).

## Done when

- The Workspace holds the intended structure and/or behaviour at the versions
  you just wrote (or a clear refusal the person must clear).
- You cited what changed relative to the prior `get`.
- You have not implied ratification of claim, spend caps, or external effects
  that still need a person.
