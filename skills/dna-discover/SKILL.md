---
name: dna-discover
description: Turn a source — a transcript, a document, a set of notes, a recorded call — into a layer 1 intent with its provenance and the questions it raises. Use when the input is prose you did not author rather than a request addressed to you, or when asked what a document implies for a model. Not for a request made directly to you; that is already an intent.
---

# Discover

One job: get the source to yield **an intent, its clauses, and the questions nobody has answered
yet** — and stop there.

Stopping there is the point. The layers below are the [`programming-layers`](../programming-layers/SKILL.md)
skill's, and they are easier and more satisfying than this step, which is why this step is the one
that gets skipped. A descent begun from an intent nobody checked is a descent that will be
faithful, coherent, checkable, and about the wrong thing.

## What a source has to give you

**A statement, in the author's words.** Record `verbatim: true` only when it is a quotation. A
paraphrase is not the intent — it is your reading of the intent, and the difference is the entire
reason provenance is a field rather than a habit.

**Where it came from.** Modality (chat, document, call, ticket), the conversation or file, the turn
or section, the author, when it was recorded. A statement with no provenance is a rumour, and
everything below inherits its standing from it.

**Its clauses, separately addressable.** "Approve it before we pay, and anything over ten thousand
needs the CFO" is three constraints: an ordering, a threshold, an authority. Split them and give
each an id. A policy grounds in one clause, not in the whole sentence — and a clause nobody can
address is a clause nothing below can be shown to have discharged.

**Its outcome.** What is true afterwards that was not true before. Absent one, you cannot tell a
finished descent from an abandoned one.

## Ask before you descend

**A word the source uses is not a word you understand.** The question to ask about any business noun
is not "what does this mean" but **"does this already mean something else here?"** — the failure is
not ignorance, it is a collision nobody noticed.

The worked example turns on exactly this. Someone said *invoice*; the graph already held one, the
seller's side, with money moving the other way and a lifecycle nothing in the request applied to.
Two types, not one flag. The version built without asking has one status enum and a bug it finds in
about six months, and every mechanical check passes the whole way.

That is the failure mode this skill exists for, and no check catches it: a **plausible wrong
reading** resolves, covers, validates, and is wrong.

**Ask, do not guess.** Asking costs a turn. Guessing costs two modules that are both correct.

**Name what each question settles**, by artifact id, before you ask it. A question with named
consequences is one somebody can prioritise; a bare question is one they answer politely and you
ignore.

**Feed answers back.** An answer that does not change the artifact means the asking was decorative.

## When an answer does not come

Do not choose. Record a **deferral**: the artifact, a reason, and who declared it — and declare it
now, while producing the artifact, not after something downstream reports it missing. A deferral
added once a result names the omission is a checkbox; declared up front it is a decision.

## Done when

- The intent exists as its own reviewable artifact, with statement, outcome, source, and clauses
  that each carry an id.
- Every question you asked is recorded with its answer, or the artifact it would have settled
  carries a deferral naming why it did not.
- No artifact below layer 1 exists yet.

The last one is a real criterion, not a formality. If terms have appeared, you have started
descending on an intent nobody has confirmed, and the cost of being wrong has just been multiplied
by every rung below.
