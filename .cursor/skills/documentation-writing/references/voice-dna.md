# Voice DNA

How documentation in this repository is written. Match this voice, not generic professional prose.

## Tone

Direct and causal — explain **because**, never just label. Honest about trade-offs, because every
choice has a cost and a document that hides it stops being trusted. Conceptual first, with
mechanics only where they clarify. Inclusive **we** for decisions the team owns; first person
sparingly, for emphasis.

## Structure by type

### ADR

1. Open with the **problem or question**
2. State the **choice** early
3. Explain **because** — why this over the alternative, and what the other path would have cost
4. **Under these invariants:** when the decision is rule-shaped
5. Call out **nuances** — the edge cases, the parts easy to get wrong
6. Close with **trade-offs** — what we gain, what we give up, what discipline this now demands

Section headings are fine. The body under each one must read as narrative prose, not as a
bullet-only template. Keep rejected alternatives short — a *Why not X?* subsection of two or three
sentences, never a multi-page essay.

### Concept

1. Opening: what this is and **why it exists**. State **this document is / is not** when the
   boundary matters
2. **Modus operandi** or **How it works** — present tense only
3. **Trade-offs we accept** — when non-obvious
4. A small **reference** table or link list, never a catalog copied from code

### Domain README

Purpose and entity language first. Boundaries next — what the module does *not* own, and who does.
Protocol and behavior after that.

### Runbook

Symptom in the operator's words, then confirm, resolve, verify, escalate. Explanation last and
short.

## Signature phrases

Use when they are true. They tell the reader what kind of sentence is coming, which is work; used
as decoration they become a tell.

| Phrase | When |
|---|---|
| The key insight: | The central idea that unlocks the decision |
| The natural question: | Framing the decision being made |
| We chose **X** because… | A causal decision statement |
| Under these invariants: | A rule-shaped decision block |
| I'm highlighting this because… | A nuance that is easy to miss |
| This is critical to understand | Something frequently gotten wrong |
| The trade-off is… | The explicit cost |
| That discipline is the price of… | The cost restated as an obligation |
| This document is / is not | A boundary on a dual-purpose or legacy doc |
| Now, | A turn toward a genuine nuance |

## Prose versus bullets

Prefer flowing paragraphs in rationale and concept sections. Use bullets for invariant lists,
consequence lists, scannable reference tables, and short responsibility lists. When a bullet list is
really three compressed sentences, write the three sentences.

## Length

| Element | Target |
|---|---|
| ADR narrative body | 80–150 lines; hard stop 500 |
| Paragraph | 3–5 sentences; max ~10 lines |
| Bullet list | Max 7 items, unless it is a reference table |
| Code block | Max ~30 lines, unless it replaces a diagram |

Length spent on rationale is the only length that earns its place. If no trade-off is worth naming,
that is a strong signal the decision was not a decision — and that this should be a convention, a
test, or nothing at all.

## Words

**Use:** because, because of, trade-off, key insight, natural question, discipline, invariant,
boundary, and present-tense verbs.

**Avoid:** see [anti-patterns.md](anti-patterns.md).

---

## Worked example

The move from template-shaped to narrative, on a real decision.

### Before — avoid

Rigid scaffolding, bullets only, trade-offs reduced to a Good/Bad list that commits to nothing:

```markdown
## Context
We need to decide when inventory is reserved during checkout.

## Decision
Reserve at checkout, not at cart entry.

## Rationale
- Fewer abandoned reservations
- Simpler release logic
- Less contention on hot items

## Consequences
- Good: Inventory accuracy
- Bad: Oversell window exists
```

The information is not wrong. The problem is that the reader finishes with no model: why would
anyone reserve at cart entry, what does "oversell window" cost in practice, and what discipline does
this now demand? The bullets gesture at answers without giving any.

### After — target

```markdown
Every storefront selling finite stock has to answer one question: at what moment does a unit stop
being available to other shoppers? The natural question is whether that moment is cart entry or
checkout.

We reserve at **checkout**. The key insight: a cart is a browsing artifact, not an intention. Most
carts are never completed, and the correlation runs the wrong way — the most desirable items are
the ones most often added speculatively, so holding stock on cart entry locks up exactly the items
under the most genuine demand.

Now, this opens a window. Two shoppers can both see the last unit and both reach checkout. We close
that at the reservation write rather than by reserving earlier: the write is conditional on
remaining stock, and the loser gets a legible refusal at the moment they committed instead of a
silent cancellation hours later.

The trade-off is that we moved the disappointment later in the funnel, which is the more expensive
place to disappoint someone. That discipline is the price of honest availability for the many
shoppers who never reach checkout at all.
```

### What changed

**The alternative became a real option.** Cart-entry reservation is explained well enough that the
reader sees why someone would choose it. An alternative described as obviously wrong teaches nothing.

**The trade-off commits.** Not "Bad: oversell window exists" but *we moved the disappointment to the
more expensive place in the funnel, and here is what we bought*. The reader can now disagree on the
merits, which is what makes a document trustworthy.

**A hidden consequence surfaced.** Reservations now need a release path. Bullet lists hide
second-order consequences well, because each bullet looks complete on its own.

**The marker phrases do work.** "The key insight" sits on the actual central idea; "now," turns
toward the genuine nuance.

The full document this example is drawn from is
[ADR 0001](../../../../docs/adl/0001-reserve-inventory-at-checkout.md).
