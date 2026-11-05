---
name: dna-render
description: Show a finished set of layer artifacts as prose, a diagram, or a page a reviewer can read — text, markdown, mermaid or HTML. Use when a descent needs reviewing by someone who will not read JSON, when asked to diagram or write up a model, or when handing a descent to a person for confirmation. Not for producing the artifacts; this only displays ones that exist.
---

# Render

A descent that only exists as JSON gets confirmed by nobody. This is the step that puts it in front
of a person — and the whole of its discipline is that **the rendering must not become a second
model**.

## Render it, do not re-describe it

Write out what the artifacts say. Do not summarise, reorder by importance, drop the boring ones, or
smooth over the parts that look unfinished. A reviewer is being asked to confirm the descent, and
they can only confirm what they are shown.

The temptation is to produce a nicer document than the artifacts deserve. Resist it: a rendering
that reads better than the model is a rendering that will be approved instead of the model.

**Same artifacts in, same rendering out.** If you render twice and get two different documents, a
reviewer diffing them learns nothing. Order by layer, then by id — a stable order you did not
choose per run.

## Four formats, four readers

| Format | For |
| --- | --- |
| `text` | A terminal, a diff, a quick scan |
| `markdown` | A pull request, a document, a comment |
| `mermaid` | The shape — what caused what, as a graph |
| `html` | A standalone page someone can open |

Two rules that are not cosmetic. **Mermaid draws the citations as edges**: the diagram's job is to
show what an artifact was grounded in, because that is the structure nobody can see in a list.
**HTML fetches nothing at view time** — inline the styling. A page that needs a network to be
legible is not an artifact you can send anyone.

## Show the standings, especially the bad ones

Three standings — complete, **deferred**, **gap** — defined in
[`dna-extract`](../dna-extract/SKILL.md). Flattening them is the one failure this step can cause on
its own, so show all three distinctly, and:

**Carry a deferral's reason, in every format.** A mark without its reason stops travelling with the
artifact and gets rediscovered later as a mystery.

**Say what a gap caps.** Marking it is half the job; the useful half is what is unreachable because
of it.

A rendering where a gap looks like everything else is worse than no rendering: it converts an open
question into apparent completeness, which is exactly the thing a reviewer was brought in to catch.

## Done when

- Every artifact in the set appears, at its layer, with its id.
- Every deferral shows its reason and every gap is marked as one.
- Rendering the same set again produces the same output.

`dna-author render <descent>.json --format <format>` does all of this deterministically and is the
easiest way to get it right. It is an accelerator, not a requirement — the formats above are
ordinary documents, and writing one by hand is fine as long as it satisfies the three criteria.
