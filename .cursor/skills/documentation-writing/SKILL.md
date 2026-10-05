---
name: documentation-writing
description: >
  Decides whether a document is allowed, where it belongs, and writes or revises it in the
  project's conceptual-narrative voice.
  Trigger: When creating or revising any committed markdown under docs/, when choosing between
  ADR / concept / domain README / runbook, or when cleaning up agent-generated docs that read
  like implementation plans.
license: Apache-2.0
metadata:
  author: gentleman-programming
  version: "2.0"
---

## When to Use

- Creating or revising any committed `.md` under `docs/`
- Choosing a placement: ADR, concept, domain README, or runbook
- Reviewing a document before it lands — or deleting one that should never have landed
- Cleaning up agent-generated markdown that reads like a plan instead of a document

**Not for:** collocated runtime notes next to source (`doc.md`, local-dev `README.md` — lighter
bar), scratch planning files, or anything gitignored.

---

## The One Idea

**Code and tests are the contract.** Everything else follows from that.

A type tells the reader the fields. A test tells them how to invoke it. The API schema tells them
the payload. None of those tell the reader *why the boundary is where it is*, *what this capability
means to the business*, or *what to do at 3am when it breaks*. Documentation exists for exactly
that residue — and for nothing else.

This is the whole reason the boundary is a hard gate and not a preference. A repo that documents
what code already says accumulates a second source of truth that drifts, and a drifted document is
worse than no document: it actively misleads the next reader, human or agent.

**The so-what test.** Every paragraph must answer: *what does the reader know now that code and
tests would not have told them in five minutes?* If the answer is nothing — delete the paragraph.
Run this on your own draft before anyone else has to.

---

## Classify Before You Write

| Reader need | Type | Path | Mutable |
|---|---|---|---|
| Why we decided X, and what we rejected | ADR | `docs/adl/####-{kebab-title}.md` | No, once accepted |
| Vocabulary and modus operandi many areas share | Concept | `docs/concepts/{kebab-topic}.md` | Yes |
| How a bounded context behaves today | Domain README | `docs/domain/{module}/README.md` | Yes |
| What to run during a production incident | Runbook | `docs/runbooks/{audience}/{scenario}.md` | Yes |
| Fields, filters, wiring, how to invoke | **none** | Code, tests, API schema | — |
| Plan, roadmap, phased rollout, status | **none** | Tracker or chat | — |

Four committed types. Pick one *before* drafting — the type determines the voice, and rewriting
voice afterward is more expensive than choosing correctly up front.

`docs/concepts/` is deliberately sparse. A concept doc is justified only when the vocabulary is
genuinely cross-cutting *and* code plus rules cannot carry it. One or two per repo is healthy; a
dozen means concepts are being used as a dumping ground.

Mirror source module names under `docs/domain/`. Max four levels deep from `docs/`.

---

## The Voice

Conceptual first, narrative prose, trade-offs named. Living docs in present tense.

Full style guidance lives in [references/writing-style.md](references/writing-style.md), with a
worked before-and-after rewrite. Read it before drafting if you have not internalized the voice.

---

## Per-Type Checklists

Each type has its own bar. The detail — section-by-section checklists, the ADR content boundary,
and what to do with content that fails a type's bar — is in
[references/doc-taxonomy.md](references/doc-taxonomy.md).

The short version:

**ADR** — Status, Context, Decision, Rationale with trade-offs, Consequences. States the invariant
in product and architecture terms. Carries **no** type names, no code blocks, no file paths, no
table or column names, no config snippets. Immutable once accepted: when expectations change, a
new ADR supersedes it and living docs get updated.

**Concept** — Opens with what this is and why it exists. A *modus operandi* section describing
behavior in present tense. Trade-offs we accept, when non-obvious. A small reference table at the
end only if it carries something metadata cannot.

**Domain README** — What the module owns and where its boundaries are. Links out to the ADRs that
constrain it. Never a catalog of every endpoint, field, or registration key.

**Runbook** — Symptom, how to confirm it, the ordered steps, how to verify recovery, and when to
escalate. The only type where step-by-step commands are correct.

---

## Workflow

1. **Intake** — intent (new / revise / review / delete), target path, related ADRs, explicit
   non-goals. If the intent is "document the work I just did", stop: that is usually a tracker
   entry, not a document.
2. **Gate** — run the ladder in `.cursor/rules/docs-boundary.mdc`. Writing no document is a valid
   and frequently correct outcome.
3. **Read a sibling** — open the nearest existing document of the same type and match its shape.
   Consistency of form is what makes the corpus navigable.
4. **Outline** — section headings only. Confirm the path and type with the requester before prose.
5. **Draft** — present tense for living docs; narrative rationale for ADRs.
6. **Review** — so-what test on every paragraph. Strip anything the IDE already shows. Verify no
   links point at gitignored or transitional files.

---

## Review Heuristics

When reviewing someone else's document — or your own — these are the failures that actually recur:

| Smell | What it means | Fix |
|---|---|---|
| A field or operator table | Copied from types or metadata | Delete; link to the type |
| "Phase 1 / Phase 2", "slice N" | A plan wearing a document's clothes | Move to the tracker |
| Type or class names in an ADR | Implementation leaked into a why-doc | Describe the role, not the symbol |
| "We used to…" in a living doc | History in a present-tense doc | Delete, or move to the ADR |
| A file per class or endpoint | Per-component documentation | Delete; one module README |
| Bullet lists three levels deep | Compressed prose avoiding commitment | Rewrite as paragraphs |

---

## Resources

- **Taxonomy and per-type checklists**: [references/doc-taxonomy.md](references/doc-taxonomy.md)
- **Voice, with worked example**: [references/writing-style.md](references/writing-style.md)
- **ADR format and content boundary**: [references/adr-format.md](references/adr-format.md)
- **The gate (always-on rule)**: `.cursor/rules/docs-boundary.mdc`
- **Live examples**: every document under `docs/` is a reference implementation of its type
