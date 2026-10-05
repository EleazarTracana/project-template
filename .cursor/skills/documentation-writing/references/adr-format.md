# ADR Format and Content Boundary

ADRs live in `docs/adl/`, numbered sequentially with four digits. Valid statuses: `PROPOSED`,
`ACCEPTED`, `SUPERSEDED`.

Do not act on a `PROPOSED` ADR without confirming with the requester — it records an intent, not an
agreement. Do not let a `SUPERSEDED` ADR inform a decision; follow the ADR that replaced it.

## Format

Keep the lightweight shape. Status, Context, Decision, Rationale with trade-offs, Consequences,
References. Resist heavier templates with Decision Drivers, per-option pro/con matrices, and
Confirmation sections: they produce documents that are filled in rather than written, and a filled-in
form does not transfer a mental model.

The sections are scaffolding for the reader's benefit, not a form to complete. Inside each one,
write prose.

## The boundary test

An ADR captures **why** the platform decided something, never **how** it is implemented. Apply this
to every sentence:

> Would this sentence change if we refactored the implementation without changing the architectural
> intent?

If yes, it does not belong in the ADR.

## Belongs in an ADR

- The decision, stated as an invariant the platform must preserve
- The boundary it establishes or moves
- The alternatives genuinely considered, and what each would have cost
- The trade-offs accepted, and the discipline they now demand
- Cross-cutting constraints every consumer of the decision inherits

## Does not belong in an ADR

| Leak | Write instead |
|---|---|
| Type, interface, or class names | The role — "the reservation writer", not `IReservationWriter` |
| Code blocks of any kind | Prose describing the behavior |
| Table or column names | The relationship — "many-to-many through a join", not the schema |
| Queries, SQL or otherwise | Nothing; query patterns are a living-doc or rule concern |
| Endpoint URLs, SDK symbols | The capability, named in business terms |
| Step-by-step runtime sequences | A paragraph describing the flow's shape |
| Config snippets, env vars | Nothing; configuration is not architecture |
| File or project path listings | Nothing |
| One module's business rules | That module's README |

## Where removed content goes

| Content | Destination |
|---|---|
| Registration patterns, wiring examples | A cursor rule |
| Schema shape, query patterns | Living doc collocated with the module |
| Decision trees, agent guidance | A skill under `.cursor/skills/` |
| Endpoint shape and payloads | API schema metadata on the endpoint itself |
| Operational steps | A runbook |
| Timeline, phases, pending work | The tracker — not the repo |

## Is this actually an ADR?

Four questions. A no on any of the first two usually means this is not an ADR.

1. Does it establish or change a system boundary? If not, it is probably a feature spec.
2. Does it constrain how future components get built? If not, it is a one-time implementation choice.
3. Could it change for one module without changing the architecture? If yes, it is a rule or a living doc.
4. Is it cross-cutting across modules or shared platform behavior? If yes, it is an ADR.

A trade-off evaluation on a single feature is not an ADR. An ADR establishes an invariant the
platform must preserve.
