---
name: dna-sync
description: Sync finished structure/v1 and behaviour/v1 documents to a DNA Workspace via the shipped MCP tools (list, get, validate, apply, diff). Use when asked to save, push, write, or apply DNA to the server after drafting. Not for discovering or inventing the model (use dna-operational), and not for looking up existing rules mid-task without writing (use business-rules).
---

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

## Shipped tools only

| Tool | Role |
| --- | --- |
| `describe` | Grammar, definition schemas, tool list |
| `list` | Domains and behaviour present in the org |
| `get` | Fetch a domain or behaviour document |
| `validate` | Check a document before writing |
| `apply` | Write DNA (governed; may need approval or claim) |
| `diff` | Diff a document against the stored version |
| `set-egress-allowlist` | After claim — destinations effects may leave to |

Do **not** call `dna_search`, `dna_describe`, or `dna_start`.

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
with existing DNA.

## Done when

- The Workspace holds the intended structure and/or behaviour at the versions
  you just wrote (or a clear refusal the person must clear).
- You cited what changed relative to the prior `get`.
- You have not implied ratification of claim, spend caps, or external effects
  that still need a person.
