# OM-1: The Operational Model

**Version 0.1 — Draft.** A vendor-neutral specification for the abstraction layer between an ontology and an execution surface.

This document proposes that the Operational Model become a first-class layer of software engineering, and defines the minimum an implementation must provide to claim conformance. It does not claim to have invented operations, actors, preconditions, or effects — see [Prior art](#prior-art). It claims that these belong in one layer, with checkable handoffs to the layers above and below, rather than scattered across five disconnected practices.

The key words MUST, MUST NOT, SHOULD, and MAY are to be interpreted as described in RFC 2119.

---

## 1. The problem

Between "a customer should be able to pay an invoice" and a working program, a dozen decisions get made: what a customer is, what paying does to an invoice, who may do it, what happens when the amount is short. Today those decisions are made during code generation and recorded only in the code that resulted from them.

Every other layer of the stack has somewhere canonical to live. Semantics has a glossary. Ontology has a schema. Execution has an OpenAPI document. Operations have nowhere, so they dissolve — a precondition here, a status transition there, a permission check three files away.

That is the gap this specification addresses.

## 2. The layers

A conforming description of a system distinguishes eight layers. Each answers exactly one question and produces exactly one kind of artifact.

| #   | Layer                 | Question it answers                             | Artifact           |
| --- | --------------------- | ----------------------------------------------- | ------------------ |
| 1   | Intent                | What do we want to accomplish?                  | Intent             |
| 2   | Semantics             | What do the words mean?                         | Vocabulary         |
| 3   | Ontology              | What exists, and how is it related?             | Entities and edges |
| 4   | **Operational Model** | What can happen, by whom, and with what effect? | **Operation**      |
| 5   | Execution Model       | How is an operation invoked?                    | Execution contract |
| 6   | Program               | How is it implemented?                          | Code               |
| 7   | Technology            | What frameworks and runtimes carry it?          | Stack              |
| 8   | Runtime               | How does the machine execute it?                | Machine code       |

Layers 6–8 are solved. Layer 5 is largely standardized (OpenAPI, protobuf, GraphQL SDL, AsyncAPI, JSON Schema, MCP tool definitions). Layers 2–4 are practiced but not standardized, and layer 4 least of all. This specification is normative for layer 4 and descriptive elsewhere.

## 3. The handoffs

A layer without a defined transformation into the layer below it is a modeling practice, not an abstraction layer. The handoffs are therefore part of the specification.

| From → To              | Transformation | Nature                                                            |
| ---------------------- | -------------- | ----------------------------------------------------------------- |
| Intent → Semantics     | Elicitation    | Judgment. Requires a human decision.                              |
| Semantics → Ontology   | Formalization  | Judgment, assisted.                                               |
| Ontology → Operations  | Derivation     | Judgment, assisted. Candidates are derivable; correctness is not. |
| Operations → Execution | **Projection** | **Mechanical. MUST be deterministic.**                            |
| Execution → Program    | Generation     | Mechanical for scaffolding; judgment for logic.                   |
| Program → Technology   | Binding        | Mechanical.                                                       |
| Technology → Runtime   | Compilation    | Mechanical. Solved for decades.                                   |

The dividing line matters more than the individual rows. Above operations, transformations are conversations that end in an artifact. From operations downward, they are compilations. An implementation MUST NOT require judgment to project an operation onto an execution surface; if it does, the operation is underspecified.

## 4. The Operation

The operation is the primitive of layer 4.

An operation is a named thing that can happen in a business, defined independently of any technology that exposes or implements it. `Pay Invoice` is an operation. `POST /invoices/{id}/pay` is not — that is one projection of it.

### 4.1 Required questions

An operational model MUST be able to answer all seven of these for every operation it contains:

1. **Who can perform this?** — the actor
2. **What can they act upon?** — the target
3. **What must be true first?** — the preconditions
4. **What changes?** — the effects
5. **What event occurs?** — the emitted event
6. **What can go wrong?** — the errors
7. **How is it exposed?** — the projections

Questions 1–6 MUST be authored at layer 4. Question 7 MUST be _answerable_ from layer 4 but MUST NOT be authored there — exposure is derived by projection (§3). An operational model that hand-writes its own REST paths has leaked layer 5 upward.

### 4.2 Shape

An operation MUST declare `name`, `actor`, `target`, `preconditions`, and `effects`. It SHOULD declare `events` and `errors`; an operation without them is projectable but not checkable.

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

Every identifier in `actor`, `target`, and the entity positions of `preconditions` and `effects` MUST resolve to an entity in the layer-3 ontology. This is the checkable half of the ontology → operations handoff.

### 4.3 Effects

Projection is required to be mechanical (§3), and nothing mechanical can consume prose. An effect written as `Payment marked refunded` cannot be turned into a response body, a tool schema, or a disabled button by any process that is not guessing. Effects therefore have a grammar, and v0.1 defines four forms:

| Form           | Syntax                                       | Example                          |
| -------------- | -------------------------------------------- | -------------------------------- |
| **Transition** | `<Entity>.<field> -> <State>`                | `Invoice.status -> Paid`         |
| **Assignment** | `<Entity>.<attribute> = <expression>`        | `Invoice.issued_at = now`        |
| **Creation**   | `create <Entity>`                            | `create Payment`                 |
| **Edge**       | `link <Entity>.<edge> -> <Entity>`           | `link Payment.settles -> Invoice`|

Every effect MUST match one of these forms. A transition's `<State>` MUST be declared in that entity's states, and an assignment's `<attribute>` MUST be one of its attributes — which is what makes an unresolvable effect a validation failure rather than a comment nobody reads.

Four forms will not cover every domain. The list is provisional and expected to grow from use; what is not negotiable is that the set stays closed and parseable. An escape hatch for free text would return the layer to prose and take determinism with it.

**Preconditions are deliberately left unconstrained in v0.1.** They carry comparisons, set membership, and cross-entity paths, and fixing a grammar for them now would mean inventing an expression language on no evidence. The asymmetry is principled rather than an oversight: projection *consumes* effects, so effects must parse, while preconditions mostly project as a call to the operation's guard. Expect this to tighten in a later version.

### 4.4 What an operation is not

- **Not a CRUD verb.** `UpdateInvoice` is a database operation wearing a business costume. If the name does not appear in a sentence a person in the business would say, it is probably layer 6 leaking upward.
- **Not a workflow.** A workflow orders operations; it is not one. Multi-step processes are compositions of operations, each independently valid.
- **Not an endpoint.** See §4.1, question 7.
- **Not a permission.** A permission answers "may this actor act?" An operation answers that _and_ what the act means and leaves behind. Constraints stop an agent doing damage; operations are what let it do the job at all.

## 5. Conformance

An implementation of OM-1 MUST:

1. Represent operations as first-class, addressable artifacts — not as annotations on code, endpoints, or database tables.
2. Answer all seven questions of §4.1 for every operation.
3. Resolve every entity reference in an operation against a declared ontology (§4.2).
4. Project an operation onto at least one execution surface deterministically, with no per-surface authoring of preconditions, effects, or errors.

An implementation SHOULD:

5. Validate the handoffs mechanically: every operation names an actor; every execution contract corresponds to exactly one operation; no exposed surface permits a transition the operation's preconditions forbid.
6. Detect drift in the other direction — effects present in code but absent from the model.
7. Support more than one projection target, since the value of the layer is that four surfaces cannot disagree.

An implementation MAY use any serialization. The YAML in §4.2 is illustrative, not normative.

## 6. Non-goals

This specification does not define an ontology language (use OWL, RDF, JSON Schema, or a database schema you have read carefully). It does not define a workflow language, a policy language, or a code generator. It does not require any particular runtime, and it takes no position on whether operations are authored by hand, derived by a model and confirmed, or extracted from an existing system.

## 7. Prior art

Every part of this has a tradition behind it, several of them decades old.

- **BPMN**, **statecharts** and **XState** — process and state modeling
- **Temporal**, **Camunda** — workflow engines that execute against an explicit model
- **Domain-Driven Design** — aggregates, commands, domain events, ubiquitous language
- **Authorization services** and policy engines — who may act, under what conditions
- **Formal specification** in safety-critical work — proving properties before implementation
- **OWL / RDF / SPARQL**, knowledge graphs — the ontology layer beneath this one
- **OpenAPI**, **protobuf**, **GraphQL SDL**, **MCP** — the execution layer beneath that

What none of them is: a layer the rest of the system is generated from. Each models one slice, alongside the code, reconciled by hand. A workflow engine knows the steps but not the vocabulary. A policy service knows who may act but not what the act does.

The contribution of OM-1 is not the pieces. It is the claim that they belong in one layer, with the handoff below it mechanical.

## 8. Implementations

- **DNA** ([dna.codes](https://dna.codes)) — reference implementation.

Implementations are welcome and do not require permission. If you build one, the specification is the thing to conform to, not any particular product.

---

## Appendix A: Agent instructions

Short, copy-pasteable instruction blocks that point agents at this specification live in [`instructions/`](./instructions/AGENTS.md). Claude Code skills live in [`skills/`](./skills/).

## Appendix B: Machine-readable schema

[`schemas/operation.schema.json`](./schemas/operation.schema.json) validates §4.2.
