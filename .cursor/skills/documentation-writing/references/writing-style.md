# Writing Style — Worked Example

The rule `.cursor/rules/docs-boundary.mdc` decides whether to write. The rule
`.cursor/rules/docs-writing-style.mdc` states the voice. This file shows the voice applied, because
style is one of those things that transfers by example far better than by instruction.

## The voice in one paragraph

Conceptual first. Narrative prose over bullets. Trade-offs named out loud. Living docs in present
tense. Inclusive "we" for decisions the team owns. Lead with why, because the reader can get the
what from code in five minutes.

---

## Before — avoid

Rigid scaffolding, bullet-heavy, no narrative, trade-offs reduced to a Good/Bad list that commits
to nothing:

```markdown
## Context
We need to decide when inventory is reserved during checkout.

## Decision
Reserve at checkout, not at add-to-cart.

## Rationale
- Fewer abandoned reservations
- Simpler release logic
- Less contention on hot items

## Consequences
- Good: Inventory accuracy
- Bad: Oversell window exists
```

What is wrong with it is not the information — it is that the reader finishes with no mental model.
Why would anyone have reserved at add-to-cart? What does "oversell window" cost us in practice?
What discipline does this now demand? The bullets gesture at answers without giving any.

---

## After — target style

Same decision, same facts, written so the reader leaves with a model:

```markdown
Every checkout flow has to answer one question: at what moment does a unit of inventory stop being
available to other shoppers? The two candidates are the moment it enters a cart, and the moment the
shopper commits at checkout.

We reserve at **checkout**. The key insight is that a cart is a browsing artifact, not an
intention — most carts are never completed, and reserving on cart entry means the most desirable
items spend their day locked up on behalf of shoppers who were never going to buy. Reserving at
checkout ties the lock to an actual commitment, which keeps availability honest for everyone else.

Now, this does open a window. Two shoppers can both see the last unit as available and both reach
checkout, and one of them will lose. We handle that at the reservation write rather than by
reserving earlier: the write is conditional on remaining stock, and the loser gets a clear
out-of-stock response at the moment they commit instead of a silent cancellation hours later.

The trade-off is that we moved the disappointment later in the funnel, which is the more expensive
place to disappoint someone. We pay that to keep availability accurate for the many shoppers who
never reach checkout at all. It also means reservations now need a release path — an abandoned
checkout must return its units — and that release path becomes an operational concern we own.
```

---

## What changed, specifically

The second version is longer and that is the point — length spent on rationale is the only length
that earns its place. Concretely:

**It names the alternative as a real option.** "Reserve at add-to-cart" gets explained well enough
that the reader understands why someone would choose it. An alternative described as obviously
wrong teaches nothing.

**It commits on the trade-off.** Not "Bad: oversell window exists" but *we moved the disappointment
to the more expensive place in the funnel, and here is what we bought with that*. The reader can
now disagree with us on the merits, which is what makes a document trustworthy.

**It surfaces a consequence the bullets hid.** The release path for abandoned checkouts falls out of
the decision, and the prose version catches it. Bullet lists are good at hiding second-order
consequences because each bullet looks complete on its own.

**It uses the marker phrases where they are true.** "The key insight is" sits on the actual central
idea. "Now," turns toward the genuine nuance. They are doing work, not decorating.

---

## Calibration

A good document is as long as its rationale requires and not one paragraph longer. If you cannot
find a trade-off worth naming, that is a strong signal the decision was not a decision — and that
the document should be a rule, a test, or nothing at all.
