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
| Behaviour | `behaviour/v1` | What that domain does — Actions, Operations, Workflows, Policies, Defaults |
| Access | `access/v1` | Who may do what at the organization or one workspace — groups (organization only), policies, defaults (experimental) |

Author **structure first**, then behaviour that names that domain. A set holds
at most one behaviour document per domain. Ids are kebab-case and stable once
written.

**Stay in the conversation.** Do not call `apply` without `dryRun`, and do not
read `model` unless look-up of stored DNA is needed for the craft. Optional:
`grammar` (via MCP,
when connected) for live JSON Schemas. To show a person stored DNA as a diagram,
call `render` and show its `rendering` exactly as returned; do not draw your own. Without MCP, follow the shapes below and
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

### Extending another domain's entity

An entity has **one** owning domain. Never declare `customer` twice. When a
domain needs more facts about an entity another domain owns, name that domain
in `uses` and declare an **extension**:

```json
"extensions": [
  {
    "entity": "customers/customer",
    "description": "A customer as lending sees them.",
    "attributes": [
      { "name": "creditProfile", "type": "enum", "values": ["prime", "near-prime", "subprime"] }
    ]
  }
]
```

- An extension attribute is never a `state`, and never repeats a name the
  owner (or another extension of that entity) already uses.
- This domain's behaviour may declare Actions on the extended entity that change
  **only** the extension's attributes.
- To change the owner's attributes or state, declare an Operation on an Action
  the owner declares (`customers/customer.flag-risk`), or let the owner react to
  an event. Never add an Action to another domain's entity.

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

### Policy (who may do what, and business rules)

Every rule is one policy: `{ id, description, target?, actions, when?, effect,
priority? }`. Permissions and business validation share this grammar — do not
hand-write a second rule language in code.

```json
{
  "id": "editors-release-titles",
  "description": "Editors, and agents acting for one, read and release titles.",
  "target": "distribution/title",
  "actions": ["run:read", "release"],
  "when": { "anyOf": [
    { "group": "editors" },
    { "allOf": [
      { "subject": "actor", "path": "kind", "op": "eq", "value": "agent" },
      { "group": "editors", "principal": "actingFor" }
    ] }
  ] },
  "effect": { "effect": "allow" }
}
```

- `target` is what the policy applies to, relative to the workspace: a domain
  (`distribution`) or an entity (`distribution/title`). It covers everything
  under it. Omit it to mean the document's home (its domain, workspace or
  organization). Never a record id, never an Action. Cedar and IAM call
  `target` the `resource`.
- `actions` lists named Actions (`release`, or `behaviour-id/release` from a
  document this one uses), the built-in action groups, or both. The groups are
  fixed: `design:read` / `design:write` (the model), `run:read` / `run:write`
  (records), and `administer` (policies and defaults at or under the target).
  A Design right gives no Run right, and the reverse.
- `effect` is `allow`, `deny`, `route` or `require_approval`. Every applicable
  policy counts, at any target, and the most restrictive wins: `deny`, then
  `require_approval`, then `allow`. A narrower allow cannot undo a wider deny or
  approval. Write an exception on the policy it relaxes, as a `not` in its
  `when`.
- `route` allows the act and names who performs it. When routes compete, the
  deepest target wins, then the higher integer `priority`. `priority` has no
  other use.

### Conditions

`when` is closed: predicates, `allOf`, `anyOf`, `not`, and three more forms.
No expressions.

- **Group:** `{ "group": "editors" }`, or with `"principal": "actingFor"` to test
  the person or organization an agent acts for. Groups are defined only in the
  organization access document.
- **Path reference:** a predicate's `value` may read another path instead of a
  literal: `{ "subject": "actor", "path": "id", "op": "neq", "value": {
  "subject": "target", "path": "requestedBy" } }` (separation of duties).
  `principal` is allowed on an `actor` reference.
- **Relation:** `{ "related": "commerce/shopper-places-order", "from": {
  "subject": "actor" }, "to": { "subject": "target" } }` holds when that
  structure relationship connects the two. Omit `path` for the bare subject.

### Defaults

`"defaults": [{ "default": "allow" }, { "target": "distribution/title",
"default": "deny" }]` says what happens to a request no policy governs. The
deepest declared default that contains the request applies. A new workspace
declares `deny`; a workspace that ran before defaults existed declares `allow`.

## Access (`access/v1`, experimental)

Two homes, each with its own document, named by `home`:

```json
{
  "dna": "access/v1",
  "id": "organization-access",
  "name": "Organization access",
  "description": "Our groups and organization-wide guardrails.",
  "home": "organization",
  "groups": [
    { "id": "editors", "description": "Editorial staff.",
      "when": { "subject": "actor", "path": "memberships", "op": "in", "value": ["editorial"] } }
  ],
  "defaults": [{ "default": "deny" }],
  "policies": [
    { "id": "no-agent-administers", "description": "Agents never change policies.",
      "actions": ["administer"],
      "when": { "subject": "actor", "path": "kind", "op": "eq", "value": "agent" },
      "effect": { "effect": "deny" } }
  ]
}
```

- `home: "organization"` holds `groups`, `policies` and `defaults`.
- `home: "workspace"` holds `policies` and `defaults`, never `groups`.
- A domain's own policies and defaults go in its behaviour document. A policy
  lives in the document whose home contains its target.

A group is a reusable condition over a principal; `{ "group": "editors" }` is the
same as writing its condition in place. Ask who is in each group — do not guess
the attribute it reads. A Workspace may not store access documents or enforce
these policies yet: draft them, and say so when the person asks to save.

## Non-negotiables

1. **Ask when the business is ambiguous.** Do not invent entity names, states,
   who may act, or approval rules. Gaps stay open until a person answers.
2. **Code implements; DNA defines.** A rule that changes how the business works
   changes in behaviour (or structure), then every surface inherits it.
3. **No surface hand-writes contracts.** REST paths, MCP tool shapes, and UI
   controls project from Actions — they are not a second source of truth.
4. **`external: true` is serious.** Effects that leave DNA (payment, email,
   webhook) stay refused until a sandbox is claimed; say so when you author them.

## Done when

- Structure names the entities and relationships the work needs, with stable ids.
- Behaviour covers the Actions people and agents must perform, with Operations
  for who acts on what, and Policies for allow / deny / route / approve.
- When access matters, policies name their target, actions and principals,
  groups live in the organization access document, and the default of each
  workspace is stated.
- Open questions are listed explicitly — not silently filled in.
- Documents are ordinary JSON ready for [`dna-sync`](../dna-sync/SKILL.md) **when
  the person asks to save**. This skill never writes them to a server.
- Removals and experiments stay here too — `apply` cannot delete a stored domain.
