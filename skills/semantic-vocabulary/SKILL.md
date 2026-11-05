---
name: semantic-vocabulary
description: Write down what the words mean before modelling what they are. Use when a request names a business noun or verb that has not been defined here, when two parts of a system disagree about a term, or when an integration bug turns out to be two correct programs. Not for terms already defined in the project's vocabulary — read those instead.
---

# Semantic vocabulary

Layer 2 of the [programming layers](../programming-layers/SKILL.md). One question: **what does this
word denote _here_?**

It is the layer most often skipped, because every word in the request already looks like it has a
meaning. That is exactly the problem. `customer` means the billing contact in one module and the
parent account in another, and **both programs are correct**. Nobody is wrong, nothing throws, and
the two disagree for a year.

A vocabulary is not a glossary. A glossary explains jargon to newcomers. A vocabulary is the
artifact the next layer is derived *from*: an `entity` term becomes a type, an `act` term becomes an
operation, and a `quantity` term becomes the number in a rule.

## A term

```json
{
  "label": "invoice",
  "denotes": "entity",
  "definition": "A supplier's demand that we pay for goods or services already delivered to us.",
  "distinguishedFrom": [
    { "term": "receivable", "because": "Both are called `invoice`. This one arrives; that one is sent." }
  ]
}
```

**`denotes` is a closed set** — `entity`, `attribute`, `state`, `act`, `actor`, `quantity`,
`relation` — because the next layer *selects on it*. A free-text denotation puts formalization back
to guesswork.

**`definition` says what the word denotes, not how it is stored.** "A row in the invoices table" is
a schema fact wearing a definition's clothes. If you cannot write the definition without naming a
table, you have not finished eliciting.

## The four things worth asking about

**One word, two things.** The highest-value catch in this layer, every time. When the request uses a
word the system already has, ask whether they are the same thing before assuming they are. Two terms
sharing a label must each say what they are `distinguishedFrom` and why — and if the answer is hard
to write, that is the finding.

**A verb that is two verbs.** `approve` and `pay`. `submit` and `publish`. `close` and `archive`.
Ask what the word does *not* mean here and record it: `notablyNot: "To pay it. Approval and payment
are two acts with two actors."` Collapsing them yields one permission where the business has two,
and nothing downstream can recover the distinction.

**A quantity with no unit.** "Ten thousand dollars" is not a number. Record the amount, the
currency, and the scale — and whether the bound is inclusive or exclusive. Both decisions otherwise
get made silently, in code, twice, differently.

**A word that sounds like an actor but never acts.** `supplier` feels like an actor; if nothing in
the conversation has a supplier *do* anything, it denotes an entity — a thing you hold a record of.
The day one issues something into your system, that is an act, and the term gains a sibling.

## Scope

A definition holds somewhere. Two scopes may define one word differently; what is forbidden is
defining it twice in one. If you find yourself wanting to, you have found either a distinction
worth naming or a boundary worth drawing.

## What good looks like

The test is not "is this well written." It is: **could someone derive the ontology from this without
asking you anything?** If the next layer has to come back with a question, the term that failed to
answer it is the one to fix — not the schema that got it wrong.

And when the derivation below disagrees with something already built, the vocabulary is usually
right and the thing already built is the finding. A control labelled `Toggle Automation` on an
operation named `Restock inventory` is not a labelling mistake. It is two acts on one node,
and the disagreement is how you found out.
