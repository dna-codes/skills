---
name: operational-model
description: Define operations — the named things that can happen, each with an actor, target, preconditions, effects, events, and errors. Use when business behavior is being added or changed, when a state-changing surface (endpoint, tool, workflow step, action) is written or reviewed, or when recovering the rules buried in an existing codebase.
---

# Operational model

An **operation** is a named thing that can happen in the business, defined independently of any technology that exposes or implements it.

`Pay Invoice` is an operation. `POST /invoices/{id}/pay` is one **projection** of it. `invoiceService.payInvoice()` is one implementation of it. Neither of the latter is the definition, and neither may hold a rule the operation does not.

Authoring the first operation in a project: read [SPEC.md](../../SPEC.md) §4 first — it is normative and this skill is not. After that, work from here.

## The six fields

| Field           | Question                 | Required             |
| --------------- | ------------------------ | -------------------- |
| `actor`         | Who can perform this?    | Yes                  |
| `target`        | What do they act upon?   | Yes                  |
| `preconditions` | What must be true first? | Yes                  |
| `effects`       | What changes?            | Yes                  |
| `events`        | What event occurs?       | Strongly recommended |
| `errors`        | What can go wrong?       | Strongly recommended |

A seventh question — how is it exposed? — is answered by projection and never authored here.

```yaml
operation:
  name: PayInvoice
  actor: Customer
  target: Invoice
  preconditions:
    - Invoice.status == Issued
    - Payment.amount == Invoice.amount_due
  effects:
    - create Payment
    - link Payment.settles -> Invoice
    - Invoice.status -> Paid
  events:
    - InvoicePaid
  errors:
    - AmountMismatch
    - InvoiceAlreadyPaid
    - InvoiceVoided
```

## Authoring

1. **Name it as the business says it.** Verb plus object, in the vocabulary people actually use: `Pay Invoice`, `Approve Expense`, `Ship Order`. If the natural name is `Update X` or `Set X Status`, stop — see Smells.
2. **Name the actor as a role**, not a table or a user id. If the actor is a scheduler or another system, that is still a role; name it.
3. **State preconditions as facts about entities.** `Invoice.status == Issued`, not `if (invoice.status === 'issued')`. A precondition nobody can read in ten seconds will be re-derived differently by the next consumer.
4. **Write every effect in one of the four forms** — `Invoice.status -> Paid`, `Invoice.issued_at = now`, `create Payment`, `link Payment.settles -> Invoice`. Resulting state, never steps, and never prose: `Payment marked refunded` cannot be projected onto anything and will fail validation. A transition's state and an assignment's attribute must be declared on that entity in the ontology.
5. **Emit one event, past tense**, named for what happened: `InvoicePaid`.
6. **Enumerate errors, including violated preconditions.** These become the error contract of every projection at once.
7. **Resolve every entity name against the ontology.** If one does not resolve, the ontology is incomplete — fix that first, then return.

Done when all six fields are filled for every operation the change touches, and every entity named in them exists in the ontology.

## Projection

One operation, many surfaces, all derived:

| Surface       | Derived from the operation                                                          |
| ------------- | ----------------------------------------------------------------------------------- |
| REST endpoint | Path from target, method from effect shape, body from inputs, responses from `errors` |
| MCP tool      | Name and description from the operation, input schema from target and inputs, failure modes from `errors` |
| GraphQL       | Mutation named for the operation, payload from effects                              |
| Workflow step | Task with the same preconditions and effects                                        |
| UI action     | Control enabled exactly when preconditions hold                                     |
| Agent action  | Permitted exactly when the actor matches                                            |

The test of a correct model: those surfaces cannot disagree about who may act, what must be true first, what changes, or how it fails — because none of them decided it.

Writing a precondition into a projection means the precondition belongs in the operation. That is a bug in the model, not a detail of the surface.

## Recovering operations from code

Auditing a codebase for its implicit model, look for:

- **State transitions** — anywhere a status field is assigned. Each distinct transition belongs to an operation.
- **Permission checks** — each names an actor for an operation nobody wrote down.
- **Guard clauses returning 4xx** — preconditions in exile.
- **Events, queue messages, webhooks fired after a write** — the `events` of an operation.
- **The same rule in two places** — the signature of the missing layer. The API permits a payment the UI forbids.

Report candidate operations in the shape above, not a list of code locations.

## Smells

| Smell                                         | What it means                                                                                            |
| --------------------------------------------- | -------------------------------------------------------------------------------------------------------- |
| `UpdateInvoice`, `SetStatus`, `SaveOrder`     | A CRUD verb wearing a business costume. One of them usually hides three real operations.                 |
| No actor, or actor is "user"                  | Nobody decided who may do this, so each surface will decide differently.                                 |
| Preconditions as prose                        | Not checkable. Rewrite as facts about entities.                                                          |
| Effects as implementation steps, or as prose  | Layer 6 leaked upward. Use one of the four forms.                                                        |
| An HTTP path or table name inside the operation | Layers 5 and 6 leaked upward.                                                                            |
| A multi-step process as one operation         | Decompose. A workflow orders operations; it is not one.                                                  |
| No errors                                     | Either it cannot fail — rare, and worth stating — or the failure modes are undiscovered and ship anyway. |

## Non-negotiable

Do not introduce business behavior directly in code when it belongs in the operational model. Code implements operations; it does not define them. A rule changes in the model, and every surface inherits it.

---

The full layer progression is the companion skill `programming-layers`.
