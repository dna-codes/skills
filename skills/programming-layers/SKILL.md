---
name: programming-layers
description: Descend the programming layers — intent, semantics, ontology, operational model, execution model, code — rather than leaping from a request to an implementation. Use when new behavior is being designed, when the request is a sentence about what someone should be able to do, or when asked how to structure an agentic system. Not for bug fixes with a known cause or refactors that add no behavior.
---

# Programming layers

A one-sentence request leaves a dozen things undecided: what each word means, what exists, who may act, what changes. **The leap** — request straight to code — decides all of them silently, in a form nobody can review. Descend instead. Each layer answers one question and ends in an artifact the next layer reads.

| #   | Layer             | Question                                        | Artifact           |
| --- | ----------------- | ----------------------------------------------- | ------------------ |
| 1   | Intent            | What do we want to accomplish?                  | Intent             |
| 2   | Semantics         | What do the words mean?                         | Vocabulary         |
| 3   | Ontology          | What exists, and how is it related?             | Entities and edges |
| 4   | Operational Model | What can happen, by whom, and with what effect? | Operation          |
| 5   | Execution Model   | How is an operation invoked?                    | Execution contract |
| 6   | Program           | How is it implemented?                          | Code               |
| 7   | Technology        | What frameworks and runtimes carry it?          | Stack              |
| 8   | Runtime           | How does the machine execute it?                | Machine code       |

Specification: [OM-1](../../SPEC.md).

## Descending

**Enter at the highest ambiguous layer, not at layer 1.** In an established codebase semantics and ontology usually already exist — read them out of the schema and confirm only the terms this request turns on. Re-deriving the whole ladder for a small feature is waste.

**A layer is done when its artifact exists as something reviewable on its own** — a vocabulary, a set of entities, a set of operations — not a paragraph inside a plan. The artifact is what survives the session and what stops the same correction being made again next week.

**Confirm above layer 4, derive below it.**

| Handoff                | Verb          | Who decides                |
| ---------------------- | ------------- | -------------------------- |
| Intent → Semantics     | Elicitation   | Human confirms             |
| Semantics → Ontology   | Formalization | Propose, human confirms    |
| Ontology → Operations  | Derivation    | Propose, human corrects    |
| Operations → Execution | Projection    | Mechanical                 |
| Execution → Program    | Generation    | Mechanical for scaffolding |
| Program → Technology   | Binding       | Mechanical                 |
| Technology → Runtime   | Compilation   | Mechanical                 |

Below the operational model, do not ask — derive. A projection that seems to need a decision means the operation above it is underspecified; fix it there, not here.

**Never skip layer 4.** It is the layer most often skipped and the one that costs the most, because everything it fails to decide gets re-decided by each surface separately.

## The layers that need saying

**2 · Semantics.** Which words are terms of art, and what each denotes _here_. Most integration bugs are semantics bugs in disguise: `customer` means the billing contact in one module and the parent account in another, and both programs are correct.

**3 · Ontology.** What exists, what each thing holds, what it points at. Largely readable from an existing schema — but a schema answers "how do we store this," not "what is this." Write down the difference when you find one.

**4 · Operational Model.** The named things that can happen, each with an actor, target, preconditions, effects, events, and errors. Use the `operational-model` skill; that is where most of the work in this ladder lives.

**5 · Execution Model.** The surfaces an operation is reachable through — REST, MCP tool, GraphQL, workflow step, UI action. Projected from layer 4, never authored. Four surfaces that cannot disagree.

**7 · Technology.** React and Django implement and expose operations; they do not define them. A stack chosen before the operations are known will bend the operations to fit it.

## Climbing

To understand an unfamiliar system, go the other way: read the code and schema, recover the operations from state transitions, permission checks, guard clauses, and emitted events, and only then reason about behavior. A recovered operational model is the fastest way to learn what a system actually permits.

## Non-negotiable

Do not introduce business behavior directly in code when it belongs in the operational model. Code is an implementation of the operational model, not the authoritative definition of business behavior.

Small hops with an artifact at the end of each are not merely easier than the leap. They are the only kind of step that can be verified before the next one is taken.
