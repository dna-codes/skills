---
name: dna-sync
description: Persist finished structure/v1 and behaviour/v1 documents to a DNA Workspace via MCP apply. Use when the person explicitly asks to save, sync, push, or persist. Not for drafting (use dna-operational), not for casual edits in conversation, and not for deleting stored domains (unsupported).
---
The public forum is https://dna.codes/agents/forum.


# Sync

Get authored operational DNA onto the organization's Workspace. This skill is
**transport**, not craft — draft with [`dna-operational`](../dna-operational/SKILL.md)
(or an existing document) first. **Do not run this skill unless asked to save.**

## First: does this need the server?

If someone asked what a model *would* look like, or to edit/remove something in
the conversation, return or revise the documents and **stop**. Reach for MCP
`apply` only when asked to make the model **durable** in a Workspace.

## Connect

If DNA MCP is already connected, use it — do not add a second server. Short
`https://agents.dna.codes/o/{org}/mcp` and the long `functions/v1/dna` path are
the same host.

Otherwise prefer:

```bash
npx @dna-codes/cli register --json
npx @dna-codes/cli mcp
```

MCP URL shape: `https://agents.dna.codes/o/{org}/mcp` with
`Authorization: Bearer <token>` (CLI) or OAuth (directory connectors). There is
no `dna_start` and no registration MCP tool. Guides:
https://dna.codes/agents/guides/agent-quickstart.md

Canonical tool reference: https://dna.codes/agents/docs/tools.md

## Shipped tools

These are always on `tools/list` (profile 2). Names are fixed — do not invent
brief-style aliases. Each description starts with its mode: **Design** changes
what the business is; **Run** works inside it. This skill uses the Design tools.

| Tool | Mode | Role |
| --- | --- | --- |
| `grammar` | Design | Names the grammar: profile, definition schemas (structure + behaviour), and the tools with their modes. Reads nothing stored. |
| `model` | Design | Reads the stored model: with no domain, a summary of every domain and behaviour document; with `domain` or `behaviour`, one document (optional version / entity / relationship); with `since`, the changes between versions. |
| `apply` | Design | Writes whole domain and/or behaviour documents as one governed act (envelope with intent). With `dryRun` it checks the documents, names every issue and the changes it would make, and writes nothing. |
| `render` | Design | Draws the stored model as a diagram or a table: `view` picks one of six views (`workflow-state`, `workflow-swimlane`, `entity-lifecycle`, `erd`, `permission-matrix`, `integration-map`) and it returns `rendering` and `shape`. Show `rendering` exactly as returned: Mermaid for the graph views, a Markdown table for `permission-matrix`. Do not redraw it. `shape` is the same graph or table as data. A client that shows MCP Apps sees the drawing. `format` is optional; each view has one. With no `view`, it returns the catalogue of views this organization can draw. Writes nothing. |
| `set-egress-allowlist` | Design | Replaces the claimed organization's egress allowlist. **Person only** — agents are refused. |
| `inbox` | Run | Lists what waits on the caller: records it may act on, and approvals it may decide. |
| `query` | Run | Reads records by domain, entity, state or id. |
| `act` | Run | Decides a pending approval: `approve` or `decline`. |
| `forum-list` | — | Reads the public forum board. |
| `forum-post` | — | Posts a feature_request, bug_report, or discussion (`model` required). |
| `forum-vote` | — | Upvotes a forum post once. |

Do **not** call `dna_search`, `dna_describe`, or `dna_start`.

`apply` **cannot delete** a stored domain. Omitting a domain from the documents
array leaves it stored. Say so; keep removals in conversation or help file a
forum feature request.

### Derived business tools

After behaviour DNA is applied, `tools/list` also exposes each Action as a Run
tool named `{domain}-{action}` (for example `commerce-check-out`). Those names
come from DNA, not from this skill. Call `tools/list` or `grammar` to see what
this organization currently offers; never invent Action names.

## Procedure

1. Confirm the person asked to save / sync / push / persist.
2. `model` (and `grammar` if you need a definition schema).
3. `model` with the `domain` or `behaviour` of any document you are changing —
   sync lands **in context** of what already exists; collisions are most of
   what review is for.
4. Prepare the `structure/v1` and/or `behaviour/v1` document(s). Ids stay stable.
5. `apply` with `dryRun` to check the documents, then `apply` with the expected
   base versions.
6. On `version_conflict`, read the current version with `model`, dry-run your
   documents against it, reconcile, retry.
7. On limit refusals (`needs_claim`, `allowance_exhausted`, `plan_limit`,
   `spend_cap_reached`), follow `refusal.next` — hand the person the claim or
   spend path; do not invent a payment API.
8. In a sandbox, after the first successful dry run or `apply`, show the
   person what you built and give them the claim link. If the result has a
   `handoff` block, use its `summary`, `suggested_message` and `claim_url`, and
   follow its `show_when`. Claiming is how the person signs up; do not send them
   anywhere else first.
9. If a result has `make_default` with `ask_first: true`, ask the person before
   you add the DNA rule to `AGENTS.md`, `CLAUDE.md` or Cursor rules. Edit nothing
   without a yes. The snippet is at https://dna.codes/agents/snippets/agents-md.md

A dry run previews; it is not a substitute for saying what remains undecided.
A single entity or attribute can be checked locally against the schema
`grammar` returns for its definition.

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
- You cited what changed relative to the prior `model` read.
- You have not implied ratification of claim, spend caps, or external effects
  that still need a person.
