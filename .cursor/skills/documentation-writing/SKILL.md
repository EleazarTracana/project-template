---
name: documentation-writing
description: >
  The complete documentation discipline: whether a document is allowed at all, where it belongs,
  which register it speaks in, and how to write or revise it in a conceptual-narrative voice.
  Self-contained — depends on no external rule or convention file.
  Trigger: When creating or revising any committed markdown under docs/, when choosing between
  ADR / concept / domain README / runbook, when auditing prose for AI tells, or when cleaning up
  agent-generated docs that read like implementation plans.
license: Apache-2.0
metadata:
  author: gentleman-programming
  version: "4.0"
---

## When to Use

- Creating or revising any committed `.md` under `docs/`
- Deciding whether a document should exist at all
- Choosing a placement: ADR, concept, domain README, or runbook
- Auditing a draft for AI tells before it lands — or deleting one that should never have landed
- Cleaning up agent-generated markdown that reads like a plan instead of a document

**Not for:** collocated runtime notes next to source, reference-contract material that lives with
the code it describes, scratch planning files, or anything gitignored.

This skill is the whole discipline. It carries its own gate, so it needs no companion rule or
convention file to function.

---

## The One Idea

**Code and tests are the contract.** Everything else follows from that.

A type tells the reader the fields. A test tells them how to invoke it. The API schema tells them
the payload. None of those tell the reader *why the boundary is where it is*, *what this capability
means to the business*, or *what to do at 3am when it breaks*. Documentation exists for exactly
that residue — and for nothing else.

This is why the gate below is hard rather than advisory. A repository that documents what code
already says accumulates a second source of truth that drifts, and a drifted document is worse than
no document: it actively misleads the next reader, human or agent.

**The so-what test.** Every paragraph must answer: *what does the reader know now that code and
tests would not have told them in five minutes?* If the answer is nothing, delete the paragraph.
Run it on your own draft before anyone else has to.

**Encode decisions in conventions, not prose.** A small repeatable decision belongs in whatever
authoring convention the project already enforces — a linter config, a template, a test — not in a
new page of prose. A convention is the *final state* of a decision; an ADR is the narrative of
reaching it. Architectural boundaries earn both.

**When code and a document disagree, the document is wrong** — unless the code is the bug.
Documentation is descriptive of intent, never a second implementation spec.

---

## The Gate (HARD)

Run this before writing anything. Answer in order, stop at the first match.

```
Code, a test, a type, or the API schema already answers it?  → Write no document
Records a decision with alternatives?                        → docs/adl/####-{kebab-title}.md
Step-by-step production operations?                          → docs/runbooks/{audience}/{scenario}.md
Current behavior of a bounded context?                       → docs/domain/{module}/README.md
Cross-cutting vocabulary many areas share?                   → docs/concepts/{kebab-topic}.md  (rare)
A formula, rounding rule, or field contract?                 → Reference contract, beside its code
Implementation plan or pending work?                         → Tracker — not the repository
```

**Writing no document is a valid and frequently correct outcome.** It is the most common correct
outcome. Treat the first line of that ladder as the default and work downward only when it fails.

**Never commit transitional documentation.** Plans, roadmaps, phased rollouts, work-item
narratives, status trackers, and "when added" placeholders all live in the tracker or in chat.
A repository holds current behavior and accepted decisions. Nothing in transition. That is the one
line with no exceptions.

### Reject on sight

- Per-class or per-endpoint markdown
- Field, filter, or operator tables inside `docs/` — a reference contract in the wrong place
- Prose that repeats what the IDE already shows
- Migration narrative in a living document — living documents describe today
- Implementation checklists that repeat what tests already require

### Structural limits

Mirror source module names under `docs/domain/`. Nothing nests more than four levels from `docs/`.
ADRs are immutable once accepted; the other three types are living and describe the present.

---

## Read Order

Before drafting, read in this order. Do not work from memory.

1. This file — the gate, the taxonomy, the workflow
2. [references/registers.md](references/registers.md) — pick the type and its register
3. [references/voice-dna.md](references/voice-dna.md) — tone, structure, signature phrases, length
4. [references/anti-patterns.md](references/anti-patterns.md) — the failure modes to avoid
5. The nearest existing document of the same type under `docs/` — the live model

---

## Classify Before You Write

| Reader need | Type | Path | Mutable |
|---|---|---|---|
| Why we decided X, and what we rejected | ADR | `docs/adl/####-{kebab-title}.md` | No, once accepted |
| Vocabulary and modus operandi many areas share | Concept | `docs/concepts/{kebab-topic}.md` | Yes |
| How a bounded context behaves today | Domain README | `docs/domain/{module}/README.md` | Yes |
| What to run during a production incident | Runbook | `docs/runbooks/{audience}/{scenario}.md` | Yes |
| Fields, filters, wiring, how to invoke | **none** | Code, tests, API schema | — |
| A formula, rounding rule, or field contract | **none in `docs/`** | Reference contract, beside its code | — |
| Plan, roadmap, phased rollout, status | **none** | Tracker or chat | — |

Four committed types. Pick one *before* drafting — the type selects the register, and rewriting
register afterward costs more than choosing correctly up front.

`docs/concepts/` is deliberately sparse. A concept document is justified only when the vocabulary is
genuinely cross-cutting *and* the code cannot carry it. One or two per repository is healthy; a
dozen means concepts have become a dumping ground.

---

## Per-Type Checklists

The short version below; the section-by-section detail is in
[references/doc-taxonomy.md](references/doc-taxonomy.md).

**ADR** — Status, Context, Decision, Rationale with trade-offs, Consequences, References. States the
invariant in product and architecture terms. Carries no type names, code blocks, paths, schema
names, or config. Rejected options get short *Why not X?* subsections. Immutable once accepted: when
expectations change, a new ADR supersedes it.

**Concept** — Opens with what this is and why it exists. A *modus operandi* section in present
tense. Trade-offs we accept, when non-obvious. A small reference table only if it carries what
metadata cannot.

**Domain README** — What the module owns, then what it explicitly does not own and who does. Links
to the ADRs that constrain it. Never a list of endpoints, fields, or registration keys.

**Runbook** — Symptom, confirm, resolve, verify, escalate. The explanation goes last and stays
short. The only type where step-by-step commands are correct.

---

## Workflow

**Phase 0 — Intake.** Establish intent (new / revise / review / delete), type and register, the
question the document must answer, and explicit non-goals. If the intent is "document the work I
just did", stop: that is almost always a tracker entry.

**Phase 1 — Gate.** Run the ladder above. Stop here when it returns *write no document*, and say so
rather than writing something weaker.

**Phase 2 — Load voice.** Follow the read order above, with the register from Phase 0 selecting
which existing document to read as the model.

**Phase 3 — Outline.** Section headings only, plus the one-sentence justification for the type and
the explicit non-goals. Confirm path and type with the requester before writing prose.

**Phase 4 — Draft.** Present tense for living documents; causal narrative for ADRs. Apply the
so-what test per paragraph while drafting, not after. No scope creep beyond intake.

**Phase 5 — Voice audit.** Check the draft against [references/anti-patterns.md](references/anti-patterns.md).
Fix and re-audit. Do not claim the audit passed on the first pass.

---

## Review Heuristics

The failures that actually recur, in review and in self-review:

| Smell | What it means | Fix |
|---|---|---|
| A field or operator table in `docs/` | Reference contract in the wrong place | Move it beside the code |
| "Phase 1 / Phase 2", "slice N" | A plan wearing a document's clothes | Move to the tracker |
| Type or class names in an ADR | Implementation leaked into a why-document | Name the role, not the symbol |
| "We used to…", "formerly", "currently" | History or hedging in a present-tense document | Delete, or move to the ADR |
| A file per class or endpoint | Per-component documentation | Delete; one module README |
| Bullets three levels deep | Compressed prose avoiding commitment | Rewrite as paragraphs |
| "Since X, we chose Y" | Weak causal link | **because** |
| Invented category words | Taxonomy coined to sound architectural | Use the codebase's own term |
| Multi-page rejected alternatives | Steelmanning past the point of value | A short *Why not X?* subsection |

---

## Adopting This Into a Project

Copy the whole skill folder. Then adjust two things and nothing else:

1. **The paths** in the gate and the classification table, if the project's folder tree differs.
2. **The live models** under `docs/` — replace them with the project's own documents as they
   accumulate.

The references carry voice and intent, which is repository-agnostic; leave them alone. This file
carries taxonomy and gates, which are repository-specific; that is the part you edit.

---

## Resources

- **Registers — voice by type**: [references/registers.md](references/registers.md)
- **Voice DNA, with worked example**: [references/voice-dna.md](references/voice-dna.md)
- **Anti-patterns and transformation pairs**: [references/anti-patterns.md](references/anti-patterns.md)
- **Taxonomy and per-type checklists**: [references/doc-taxonomy.md](references/doc-taxonomy.md)
- **ADR format and content boundary**: [references/adr-format.md](references/adr-format.md)
- **Live models**: every document under `docs/` is a reference implementation of its type
