# Doc Taxonomy

Four committed types, each with a different job and a different bar. This file carries the detail
the skill summarizes.

## Why exactly four

The taxonomy is closed on purpose. Each type answers one reader question that code cannot:

- **ADR** — *why is the boundary here?* Answered once, then frozen.
- **Concept** — *what does this word mean, and how does the mechanism work?* Answered for the whole repo.
- **Domain README** — *how does this bounded context behave today?* Answered per module.
- **Runbook** — *what do I run right now?* Answered per incident scenario.

A document that does not answer one of those four questions does not belong in `docs/`. The key
insight is that a closed taxonomy is what keeps the corpus navigable — a reader who knows the four
types can find anything, and an agent that knows the four types cannot invent a fifth folder.

---

## ADR

**Path** `docs/adl/####-{kebab-title}.md` — four-digit sequential, never reused.
**Mutable** No. Once accepted, the body is frozen.
**Statuses** `PROPOSED`, `ACCEPTED`, `SUPERSEDED`.

### Sections

- **Status** — one of the three, plus the superseding ADR number if applicable
- **Context** — the problem and the forces, in prose
- **Decision** — stated early and clearly, as an invariant the platform must preserve
- **Rationale** — why this over the alternatives, and what each alternative would have cost
- **Consequences** — what we gained, what we gave up, what discipline this now demands
- **References** — related ADRs and concept docs, as links only

### Checklist

- [ ] The decision establishes or changes a system boundary — if not, this is a feature spec
- [ ] It constrains how future components get built — if not, it is a one-time choice, not an ADR
- [ ] It would survive a refactor that preserved the architectural intent
- [ ] It is cross-cutting, not specific to one module's business rules
- [ ] The alternatives section names a real rejected option, with its real cost
- [ ] No type names, code blocks, file paths, schema names, queries, or config snippets
- [ ] References link out without recapping the linked document's content

### Lifecycle

An accepted ADR is a historical record, not a living document. When reality changes, write a new
ADR that supersedes it and update the affected living docs. Editing an accepted ADR destroys the
record of what was actually decided and when — which is the only thing an ADR is for.

Content that fails the ADR bar still has a home: see
[adr-format.md](adr-format.md#where-removed-content-goes).

---

## Concept

**Path** `docs/concepts/{kebab-topic}.md`
**Mutable** Yes — it describes today.

A concept doc earns its place when a word carries weight across several modules and neither the
code nor a rule can teach it. That is rarer than it feels. Before writing one, check whether the
vocabulary actually appears in more than one bounded context; if it lives in one, it belongs in
that module's README.

### Sections

- **Opening paragraph** — what this is and why it exists, before any mechanism
- **Why / rationale** — the central idea, with a pointer to the ADR for the full decision record
- **Modus operandi** (or *How it works*) — the behavior, present tense, in prose
- **Trade-offs we accept** — only when non-obvious, and only when honestly costly
- **Reference** — a small table or link list, if and only if it carries what metadata cannot

### Checklist

- [ ] The vocabulary genuinely spans more than one module
- [ ] The opening paragraph works for someone who has never seen the codebase
- [ ] Mechanism is present tense and narrative, not a numbered runtime sequence
- [ ] Any reference table carries information not already in code or metadata
- [ ] Rationale links to the ADR instead of restating it

---

## Domain README

**Path** `docs/domain/{module}/README.md` — folder name mirrors the source module.
**Mutable** Yes — it describes today.

This is the document a newcomer reads to learn what a bounded context owns. Its value is the
boundary, not the inventory. The moment it starts listing endpoints, fields, or registration keys,
it has become a catalog that will drift out of date within a sprint.

### Sections

- **What this module owns** — the responsibility, in one or two paragraphs
- **Boundaries** — what it explicitly does *not* own, and which module does
- **How it behaves today** — the shape of the main flows, in prose
- **Decisions that constrain it** — links to ADRs
- **Recipes** — links to any `how-to-*.md` that lives with the module's source

### Checklist

- [ ] Folder name matches the source module name exactly
- [ ] States what the module does *not* own, naming the owner
- [ ] No endpoint list, field table, or registration-key catalog
- [ ] No migration narrative — present state only
- [ ] Links to constraining ADRs rather than restating their rationale

---

## Runbook

**Path** `docs/runbooks/{audience}/{scenario}.md` — audience is who gets paged, not who wrote it.
**Mutable** Yes — it describes the current operational path.

The one type where step-by-step commands are correct, because the reader is under time pressure
and needs to execute, not understand. Write it so it can be followed at 3am by someone who did not
build the system.

### Sections

- **Symptom** — what the operator actually sees: the alert text, the dashboard, the user report
- **Confirm** — how to verify this is the scenario before acting
- **Resolve** — numbered steps, each a single action with the command and its expected output
- **Verify** — how to know recovery actually happened
- **Escalate** — who to wake, and the threshold that justifies waking them
- **Why this happens** — one short paragraph, last, linking to the ADR or concept

### Checklist

- [ ] Symptom matches the literal alert or dashboard text an operator will search for
- [ ] Every step is one action with a copy-pasteable command
- [ ] Each step states the expected output, so a deviation is visible
- [ ] Destructive steps are flagged, with the condition that justifies them
- [ ] Escalation names a role and a threshold, not just "ask the team"
- [ ] Explanation is last and short — the operator is not here to learn
