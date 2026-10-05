# ADR 0000: Record architecture decisions

## Status

ACCEPTED

## Context

Architectural decisions outlive the people who make them and the conversations that produce them.
A boundary gets drawn in a design discussion, the code lands, and six months later someone finds
the boundary inconvenient. With no record, they face a choice between two bad options: respect a
constraint whose reasoning they cannot see, or route around it and quietly undo work they do not
understand.

This problem has sharpened as agents participate in the codebase. An agent reads code well and
infers intent badly. Shown a boundary with no stated reason, it will optimize the boundary away —
confidently, and with a clean diff.

## Decision

We record every architectural decision as a numbered document in this folder, and we treat an
accepted record as immutable.

A decision qualifies when it establishes or moves a system boundary and constrains how future
components are built. A trade-off evaluation inside a single feature does not qualify; that is
ordinary design work, and recording it would dilute the set until nobody reads it.

Each record captures the decision as an invariant, the alternatives genuinely considered, what each
alternative would have cost, and the discipline the choice now demands. It captures none of the
implementation: no symbols, no schema, no configuration. Those change without the architecture
changing, and a record that contains them rots while the decision it describes stays valid.

## Rationale

The obvious alternative is to let the code be the record. It is appealing — there is nothing to
maintain and nothing to drift. But code answers *what* with total precision and answers *why* not
at all, and *why* is exactly what a person needs before they are safe to change something. We
rejected it for that reason.

The second alternative is a mutable architecture document that always describes the current system.
We rejected that too, for a subtler reason: a document that is edited in place loses the sequence.
It can tell you the system reserves inventory at checkout, but not that the team considered and
rejected reserving at cart entry, nor what they were worried about when they chose. That history is
the part that prevents the decision being relitigated every year by people rediscovering the same
arguments. Immutability is what preserves it.

The key insight is that a decision record and a living document are different artifacts with
different update rules, and conflating them destroys the one property that makes a record useful.
When reality changes, we write a new record that supersedes the old one and update the living docs.
The superseded record stays exactly as written.

## Consequences

We gain a boundary that defends itself. A constraint with a visible reason is one a newcomer can
respect or argue against on the merits, and one an agent is far less likely to optimize away.

We pay for it with discipline at two moments. First, when the temptation arrives to edit an
accepted record because a detail has become untrue — that is the moment to write a superseding
record instead. Second, when the temptation arrives to record something that is really a feature
decision; every low-value record raises the cost of reading the set, and a set nobody reads
protects nothing.

The numbering is permanent and never reused, which means the sequence has gaps when a proposed
record is abandoned. We accept the gaps; renumbering would break every inbound link.

## References

- `.cursor/rules/docs-boundary.mdc` — when a document is allowed at all
- `.cursor/skills/documentation-writing/references/adr-format.md` — the format and content boundary
