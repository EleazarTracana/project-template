# Registers

A register is the voice a document speaks in. Pick one in intake, before drafting, and do not mix
two registers inside one document. Mixing them is the most common reason a draft reads wrong even
when every fact in it is correct.

## The two registers

**Narrative voice.** Causal prose, trade-offs named, the so-what test applied per paragraph. All
four committed types in `docs/` speak this register. Code identifiers are a smell here, because the
document's job is to carry what code cannot.

**Reference contract.** Dense bullets, tables, and code identifiers are expected, because the
document's job is to *be* the source of truth for a contract — formulas, rounding rules, field
derivations, error codes. The narrative rules do not apply.

Reference contract material does not belong in `docs/`. It lives collocated with the code it
describes, where it can be reviewed in the same change that alters the contract. This is critical
to understand because the failure is subtle: a formula table is a perfectly good document in the
wrong place, and once it sits in `docs/` next to the ADRs, readers start treating it as
architecture rationale — which it is not, and which it fails at.

A reference contract still fails the so-what test if it pretends to be rationale. The test for
which register you are in: *is this document the contract, or does it explain a contract that lives
elsewhere?*

## Voice by type

| Type | Voice | Code detail in prose |
|---|---|---|
| ADR (typical) | Question → decision → because → trade-offs | Role names acceptable; no paths, no symbols |
| ADR (invariant-shaped) | Decision block, **Under these invariants:**, then rationale prose | Invariants as bullets; rationale as prose |
| ADR (rejected path) | Sharp question, **The key insight:**, short *Why not X?* subsections | Same as typical |
| Concept | What and why → modus operandi → trade-offs | Small reference table at the end only |
| Domain README | Purpose first, entity language, then boundaries | Module ownership and capability names acceptable |
| Current-state explainer | Present the pain honestly; **I'm highlighting this because…** | Describe behavior, never stack traces |
| Runbook | Imperative, one action per step, expected output stated | Commands and identifiers expected — the reader executes |
| Reference contract | Bullets, tables, formulas | Type and field names expected — and it lives with the code |

The runbook is the one committed type that sits between the registers. Its resolve steps are
reference-contract in voice because the reader is executing under time pressure; its closing
explanation returns to narrative. Keep the explanation last and short.

## Choosing

```
Does this document BE the contract?        → Reference contract — collocate with code, not docs/
Why did we decide this?                    → ADR, narrative
What does this word mean across modules?   → Concept, narrative
How does this module behave today?         → Domain README, narrative
What do I run right now?                   → Runbook, imperative
```

If two registers both seem to fit, the document is doing two jobs. Split it.
