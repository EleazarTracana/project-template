---
name: documentation-writing
description: >
  Decides whether a document is allowed, where it belongs, which register it speaks in, and writes
  or revises it in this project's conceptual-narrative voice.
  Trigger: When creating or revising any committed markdown under docs/, when choosing between
  ADR / concept / domain README / runbook, when auditing prose for AI tells, or when cleaning up
  agent-generated docs that read like implementation plans.
license: Apache-2.0
metadata:
  author: gentleman-programming
  version: "3.0"
---

## When to Use

- Creating or revising any committed `.md` under `docs/`
- Choosing a placement: ADR, concept, domain README, or runbook
- Auditing a draft for AI tells before it lands — or deleting a document that should never have landed
- Cleaning up agent-generated markdown that reads like a plan instead of a document

**Not for:** collocated runtime notes next to source, reference-contract material that lives with
the code it describes, scratch planning files, or anything gitignored.

---

## The One Idea

**Code and tests are the contract.** Everything else follows from that.

A type tells the reader the fields. A test tells them how to invoke it. The API schema tells them
the payload. None of those tell the reader *why the boundary is where it is*, *what this capability
means to the business*, or *what to do at 3am when it breaks*. Documentation exists for exactly
that residue — and for nothing else.

This is why the boundary is a hard gate rather than a preference. A repository that documents what
code already says accumulates a second source of truth that drifts, and a drifted document is worse
than no document: it actively misleads the next reader, human or agent.

**The so-what test.** Every paragraph must answer: *what does the reader know now that code and
tests would not have told them in five minutes?* If the answer is nothing, delete the paragraph.
Run it on your own draft before anyone else has to.

**Encode decisions in rules, not prose.** A small repeatable decision belongs in an authoring rule,
not a document — extend an existing rule rather than writing a new page. A rule is the *final state*
of a decision; an ADR is the narrative of how it was reached. Architectural boundaries get both.

**When code and a document disagree, the document is wrong** — unless the code is the bug.
Documentation is descriptive of intent, never a second implementation spec.

---

## Read Order

Before drafting, read in this order. Do not work from memory.

1. This file — the gate and the taxonomy
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
| A formula, rounding rule, or field contract | **none in `docs/`** | Reference contract, collocated with its code | — |
| Plan, roadmap, phased rollout, status | **none** | Tracker or chat | — |

Four committed types. Pick one *before* drafting — the type selects the register, and rewriting
register afterward costs more than choosing correctly up front.

`docs/concepts/` is deliberately sparse. A concept doc is justified only when the vocabulary is
genuinely cross-cutting *and* code plus rules cannot carry it. One or two per repository is healthy;
a dozen means concepts have become a dumping ground.

Mirror source module names under `docs/domain/`. Nothing nests more than four levels from `docs/`.

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
to the ADRs that constrain it. Never a catalog of endpoints, fields, or registration keys.

**Runbook** — Symptom, confirm, resolve, verify, escalate. The explanation goes last and stays
short. The only type where step-by-step commands are correct.

---

## Workflow

**Phase 0 — Intake.** Establish intent (new / revise / review / delete), type and register, the
question the document must answer, and explicit non-goals. If the intent is "document the work I
just did", stop: that is almost always a tracker entry.

**Phase 1 — Gate.** Run the ladder in `.cursor/rules/docs-boundary.mdc`. Writing no document is a
valid and frequently correct outcome.

**Phase 2 — Load voice.** Follow the read order above, with the register from Phase 0 selecting
which existing document to read as the model.

**Phase 3 — Outline.** Section headings only, plus the one-sentence justification for the type and
the explicit non-goals. Confirm path and type with the requester before writing prose.

**Phase 4 — Draft.** Present tense for living docs; causal narrative for ADRs. Apply the so-what
test per paragraph while drafting, not after. No scope creep beyond intake.

**Phase 5 — Voice audit.** Check the draft against [references/anti-patterns.md](references/anti-patterns.md).
Fix and re-audit. Do not claim the audit passed on the first pass.

---

## Review Heuristics

The failures that actually recur, in review and in self-review:

| Smell | What it means | Fix |
|---|---|---|
| A field or operator table in `docs/` | Reference contract in the wrong place | Move it beside the code |
| "Phase 1 / Phase 2", "slice N" | A plan wearing a document's clothes | Move to the tracker |
| Type or class names in an ADR | Implementation leaked into a why-doc | Name the role, not the symbol |
| "We used to…", "formerly", "currently" | History or hedging in a present-tense doc | Delete, or move to the ADR |
| A file per class or endpoint | Per-component documentation | Delete; one module README |
| Bullets three levels deep | Compressed prose avoiding commitment | Rewrite as paragraphs |
| "Since X, we chose Y" | Weak causal link | **because** |
| Invented category words | Taxonomy coined to sound architectural | Use the codebase's own term |
| Multi-page rejected alternatives | Steelmanning past the point of value | A short *Why not X?* subsection |

---

## Resources

- **Registers — voice by type**: [references/registers.md](references/registers.md)
- **Voice DNA, with worked example**: [references/voice-dna.md](references/voice-dna.md)
- **Anti-patterns and transformation pairs**: [references/anti-patterns.md](references/anti-patterns.md)
- **Taxonomy and per-type checklists**: [references/doc-taxonomy.md](references/doc-taxonomy.md)
- **ADR format and content boundary**: [references/adr-format.md](references/adr-format.md)
- **The gate (always-on rule)**: `.cursor/rules/docs-boundary.mdc`
- **Live models**: every document under `docs/` is a reference implementation of its type
