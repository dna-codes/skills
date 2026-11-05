---
name: dna-extract
description: Run a descent under a checked discipline — cite every artifact, stop at the depth that was asked for, and refuse to continue when something was invented or dropped. Use when descending the layers for real rather than sketching, when asked how far down to take a model, or when a descent needs to be shown to have kept faith with its source. Not a substitute for the per-layer skills; it governs a run that uses them.
---

# Extract

The layers themselves belong to other skills. [`programming-layers`](../programming-layers/SKILL.md)
holds the progression, [`semantic-vocabulary`](../semantic-vocabulary/SKILL.md) holds layer 2,
[`operational-model`](../operational-model/SKILL.md) holds layer 4, and [OM-1](../../SPEC.md) holds
the normative statement of all of it. Read them for *what* each rung produces.

This skill is the **discipline a run is held to**: how deep to go, what every artifact owes its
parent, and what stops the run.

## Name the ceiling before the first rung

Ask how far down this goes, and do not go further.

**The default ceiling is layer 4, the operational model.** It is the last rung where a human
decides; everything below is projection, and projecting is cheap to redo and expensive to redo
*wrongly* on a model nobody confirmed.

Deeper is legitimate when asked for — an execution surface, a program, a stack. Descending there
unasked is not thoroughness. It produces artifacts somebody now has to review, in service of a
decision they had not yet made.

Going *below the ceiling to fix something* is a mistake with a name: if a projection seems to need
a decision, the operation above it is underspecified. Fix layer 4.

## Every artifact cites the artifacts that caused it

One field, and the whole discipline rests on it: what an artifact is **grounded in**, by id, one
layer up.

- An artifact citing nothing, below layer 1, is a **fabrication** — something you added that nobody
  asked for.
- An artifact citing an id that does not resolve is standing on something that is not there.
- Layer 1 is the only rung allowed to cite nothing. Grounding stops where the person starts.

Cite only ids that exist. Asked to cite without being shown what is available, a model invents
plausible ones — so work from the actual inventory of what the descent holds, not from memory of it.

## Check each rung, and let a red stop you

Two checks, opposite directions, and both are cheap:

| Check | Asks | Catches |
| --- | --- | --- |
| Grounding, downward | Did anything get invented? | Fabrications, unresolved citations |
| Coverage, upward | Did anything get dropped? | A clause nothing below picked up |

Coverage is the one that finds real omissions, and it routes by denotation: an *act* is discharged
at layer 4, not layer 3, so do not expect a verb to become a type.

**Stop on red. Report it; do not narrate around it.** "Mostly complete, with a couple of gaps" is
the sentence this discipline exists to prevent — it reads as success and is not one.

What neither check can catch is the thing worth staying afraid of: a citation that resolves, covers
cleanly, and is still the wrong reading. Mechanically these look identical to correct ones. The only
defence is the one in [`dna-discover`](../dna-discover/SKILL.md) — having asked.

## A gap is not an error

Something nothing below reached is **capped**, not failed. The descent completes; the artifacts
downstream of the gap stay provisional and inert; the report names what is unreachable and why.

Do not close a gap by inventing the thing that would close it. A gap with a name is information. A
fabricated transition that makes the gap disappear is a lie the checks will now pass.

## Done when

- The ceiling that was asked for is reached, and nothing below it exists.
- Every artifact cites something that resolves, and only layer 1 cites nothing.
- Grounding and coverage have both been run over the finished set, and either both are clean or
  every exception is a deferral declared by the layer that produced it, with a reason.
- Every remaining gap is named, with what it caps.

If a tool is available to run the two checks for you, use it — `dna-author check <descent>.json`
does exactly this and exits non-zero on either failure. Nothing here depends on having it; the
checks are two questions you can answer by reading, and the discipline is the same either way.
