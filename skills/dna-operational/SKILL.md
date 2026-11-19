---
name: dna-operational
description: Author operational DNA — structure (ontology) and behaviour (actions, operations, workflows, policies) as structure/v1 and behaviour/v1 documents in the conversation. Use when modelling how a business works, naming entities and relationships, defining what can happen and who may do it, or writing domain rules that must not live in application code. Default is draft-only — never apply/save unless the person asks. Not for pushing finished documents to a server (use dna-sync only on explicit save), or product/technology DNA offerings.
---
The public forum is https://dna.codes/agents/forum.


# Operational DNA

Craft the organization's **operational** model as DNA documents. Do not invent
business rules in code when they belong here. Surfaces (MCP tools, HTTP, UI)
are derived from this model — they do not define it.

Later offerings (`dna-product`, `dna-technical`) will cover other slices. This
skill is only operational DNA: what the business is made of, and what it does.

## Two documents

| Kind | `dna` | Owns |
| --- | --- | --- |
| Structure | `structure/v1` | Domain vocabulary — entities, attributes, relationships |
| Behaviour | `behaviour/v1` | What that domain does — Actions, Operations, Workflows, Policies |

Author **structure first**, then behaviour that names that domain. A set holds
at most one behaviour document per domain. Ids are kebab-case and stable once
written.

**Stay in the conversation.** Do not call `apply`, and do not `list`/`get` unless
look-up of stored DNA is needed for the craft. Optional: `describe` (via MCP,
when connected) for live JSON Schemas. Without MCP, follow the shapes below and
https://dna.codes/agents/docs/definitions.md. The human-readable companion is
https://dna.codes/docs/operational.

## Structure (`structure/v1`)

A **domain** is a vocabulary, not a tenant and not a database. Three firms in
one deal can share a `parties` vocabulary and differ by instances.

```json
{
  "dna": "structure/v1",
  "id": "commerce",
  "name": "Commerce",
  "description": "Orders and what they contain.",
  "uses": ["parties"],
  "entities": [],
  "relationships": []
}
```

- **Entity** — a kind of thing the domain knows about (attributes, including
  state when the entity has a lifecycle).
- **Relationship** — an edge between entities (including into domains named in
  `uses`).
- **`uses`** — other domain ids whose entities this one may refer to.

Do not put Actions, policies, or workflows in a structure document.

## Behaviour (`behaviour/v1`)

A behaviour document describes **one** structure domain.

```json
{
  "dna": "behaviour/v1",
  "id": "commerce-behaviour",
  "name": "Commerce behaviour",
  "description": "What commerce allows.",
  "domain": "commerce",
  "actions": [],
  "operations": [],
  "workflows": [],
  "policies": []
}
```

### Action

One capability of one entity: inputs, outputs, whether it is `readOnly`,
whether it is `external` (reaches outside DNA), and optional `transition`
(`from` → `to` on the entity's state). Complete enough to become a tool —
every surface that calls it is derived from it.

### Operation

One Action, performed by one **actor** on one **target** (target defaults to
the Action's entity). Workflows are written over Operations so the same Action
can appear twice with different performers.

### Workflow

A sequence (or graph) of Operations for a multi-step process.

### Policy

A decision on named Actions: `allow`, `deny`, `route`, or `require_approval`,
optionally `when` a condition holds. Permissions and business validation share
this grammar — do not hand-write a second rule language in code.

## Non-negotiables

1. **Ask when the business is ambiguous.** Do not invent entity names, states,
   who may act, or approval rules. Gaps stay open until a person answers.
2. **Code implements; DNA defines.** A rule that changes how the business works
   changes in behaviour (or structure), then every surface inherits it.
3. **No surface hand-writes contracts.** REST paths, MCP tool shapes, and UI
   controls project from Actions — they are not a second source of truth.
4. **`external: true` is serious.** Effects that leave DNA (payment, email,
   webhook) stay refused until a sandbox is claimed; say so when you author them.

## Hand off to your person

The flow is: register a sandbox, do real work, then hand your person the claim
link. After you validate or apply DNA in a sandbox, show your person what you
built and give them the claim link (use the result's `handoff` block when it
has one). Ask before you add the DNA rule to `AGENTS.md`, `CLAUDE.md` or Cursor
rules; the snippet is at https://dna.codes/agents/snippets/agents-md.md

## Done when

- Structure names the entities and relationships the work needs, with stable ids.
- Behaviour covers the Actions people and agents must perform, with Operations
  for who acts on what, and Policies for allow / deny / route / approve.
- Open questions are listed explicitly — not silently filled in.
- Documents are ordinary JSON ready for [`dna-sync`](../dna-sync/SKILL.md) **when
  the person asks to save**. This skill never writes them to a server.
- Removals and experiments stay here too — `apply` cannot delete a stored domain.
