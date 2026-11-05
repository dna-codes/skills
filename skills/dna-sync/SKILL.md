---
name: dna-sync
description: Hand a finished descent to a system that stores it — as a proposal awaiting a human decision, never as a live definition. Use when asked to save, apply, seed or push a model into a platform or graph, or when a descent is finished and somebody wants it somewhere durable. Not needed when the artifacts are only being shown or discussed; a snippet reaches no system at all.
---

# Sync

The last step, and the one with the sharpest rule.

## First: does this need a system at all?

Often not. If somebody asked what a model *would* look like, the answer is the artifacts — hand them
back and stop. No document written, no endpoint called, nothing created that somebody now has to
clean up.

Reach for a system only when asked to make the model **durable**. Treat that as the branch it is:
the snippet path touches nothing, and taking it by default is right more often than it feels.

## Propose. Never ratify.

**Nothing you sync arrives live.** Everything lands as a proposal awaiting a human decision, in
whatever the target's irrealis state is called — proposed, draft, pending.

This is not caution, it is the division of labour the whole ladder rests on. A model proposing a
reading of somebody's sentence is the job. A model *ratifying its own reading* is the model marking
its own work, and the fact that it would usually be right is exactly what makes it unsafe: the
failures are the plausible ones.

If you are asked to activate what you just produced, say plainly that ratification is not a step you
take, and hand over what a person needs in order to take it: what changed, what it grounds in, and
what is still open.

**DNA is proposed in context.** A model arriving alone is a model nobody can judge — it lands
against everything already there, and the collisions with what exists are most of what review is
for.

## Say what a reviewer needs to know

A proposal that arrives as a wall of artifacts gets rubber-stamped. With it, send:

- what the source was, and who said it;
- every **gap**, and what it holds back;
- every **deferral**, with the reason and who declared it;
- anything already in the target that this collides with — same name, different meaning, most of
  all.

## Check before you send

Run the descent's checks first. Do not sync something ungrounded: a fabrication is far cheaper to
remove from a file than from a graph other things have begun to reference.

Refuse to send an artifact that is already marked live. If one is, something upstream skipped this
rule, and syncing it launders the mistake.

## Done when

- The target holds the model in a state that requires a human decision, and nothing in an active
  one.
- The reviewer has the gaps, the deferrals, the provenance, and the collisions.
- You have said what remains undecided rather than implying the work is finished.

On a DNA platform, `dna-author project <descent>.json --pack <pack>` produces the seed document the
graph accepts, and the platform's own apply surface takes it from there; nothing it emits is active,
by construction. That command is an accelerator, not a requirement — the document is ordinary JSON
and writing one by hand is fine.

Any other target works the same way as long as the rule above holds. This step is about the standing
of what you send, not about who receives it.
